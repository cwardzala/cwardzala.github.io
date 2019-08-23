autoscale: true
slide-transition: true

# Taking our Javascript SDK Offline

---

## Content Warning

^ autoplay animated gifs

^ there will be code examples

^ not every line is important

^ I will highlihgt large code examples

^ demos

---

![Findaway 75%](findaway-logo-white.png)

---

![All-Partners](logo-soup.png)

^ We are the world’s largest audiobook distributor

^ delivering audio to all the top players in the industry, including Apple, Google, Kobo, Scribd, and more

---

![ae-logo 100%](ae-logo@2x.png)

^ one of the ways we deliver audio

^ our distribution platform audio engine

^ AE delivers millions of hours of audiobooks to listeners all over the world

---
[.background-color: #FFFFFF]
![android fill](android.png)
![apple fill](apple.png)
![js fill](js.png)

^ AE has mobile SDKs

^ ios

^ android

^ and a JS SDK

---

![booklab fit](PNG Image.png)
![authors direct fit](PNG Image 2.png)

^ our mobile SDKs

^ provide streaming and offline audio

^ can listen anytime anywhere

---

![sdk model fit](sdk-model.png)

^ our javascript sdk

^ streaming audio

^ its acutally MP3 URLs

^ using HTML5 audio

---
![Chirp interface fit](chirp.png)

^ to play audio you need an interface

^ we dont build those interfaces

^ built by our partners

^ audiobook metadata

^ title

^ author

^ chapters

^ cover art

^ play

^ pause

^ change track

^ SDK handles loading all data

^ including list of mp3 files, more on that later

^ SDK handles logging analytics

^ SDK handles playback, track skipping

---

![Lumbergh](lumbergh.gif)

^ earlier this year

^ head of product and partners approached me about adding offline support to the SDK

^ all the time i have been maintaining

^ i had never thought that anyone using our js sdk would need to listen offline

---

![surfing fit](surf.gif)

^ _**pause for a second**_

^ turns out

^ some cases for offline playback in the browser

^ schools

^ students using chromebooks

^ need offline support for students to listen when not at school

---

![HIMYM fit](himym.gif)

^ event hough it sounded impossible

^ I took on the challenge

^ could I even cache MP3 files in a browser?

^ browsers don't have access to the file system

^ what options did I have?

---

![Library fit](library.gif)

^ I started researching what resources were available

^ there was far less than i expected

^ apparently caching MP3 files is not somthing everyone is doing

---

![MDN fit](mdn.png)
![Going Offline fit](aba-cover-26_100x@3x.png)

^ The resources that I found the most useful were MDN and the book "Going offline" by Jeremy Keith

^ MDN provided all the techincal information that I needed

^ "going offline" provided theoretical guidance, along with techincal examples

---

[.build-lists]

- Local Storage
- Cookies
- Session Storage
- IndexDB
- ServiceWorkers
- Cassette tape and Walkman

^ the possible options I found were

^ local storage, session storage, index db, and service workers

^ has anyone added offline support to their application?

^ a show of hands who thinks I used Local Storage?

^ session storage?

^ index db?

^ service workers?

---

- Local Storage
- Cookies
- Session Storage
- WebSQL
- IndexDB
- ServiceWorkers
- Cassette tape and Walkman

^ local Storage, could work.
^ Would require manually detecting offline state,
^ retireve the data from storage and return it back to the client.
^ Cannot store MP3 files.

^ cookies,
^ base 64 encode the audio and metadata and store them as strings
^ That would probably break the internet.

^ Session storage
^ same issues as Local storage
^ goes away when the browser session ends
^ not great for users going from one place to another.

^ IndexDB,
^ could work seems like a great option
^ storage limitations
^ cannot store MP3 audio.
^ We would also have to manually handle offline resolution like Local storage.

^ ServiceWorkers,
^ Has a native API to cache requests.
^ Can handle caching MP3 audio requests
^ doesnt have a storage limit.

^ cassette
^ retro
^ not viable

---

![winner fit](winner.gif)

^ service workers are my chooice

---
[.build-lists]

- Service Worker
- Client
- SDK
- caches
- cache

^ a few things to know first

^ service worker
^ this is the code running inside the browser to handle caching and resolving offline requests

^ client,
^ The thing that users see
^ utilizes the audiobook SDK
^ has some sort of playback interface

^ SDK,
^ this is the AudioEngine SDK
^ handles loading
^ audiobook metadata
^ playlists
^ provides audio playback

^ Caches,
^ in the code examples
^ SW CacheStorage API

^ Cache
^ in the code examples
^ Cache instance

---

![sw tools inline](SW Tools.png)
![cache storage inline](Cache Storage.png)

^ another thing you will see in my demos

^ Chrome Dev tools Application tab

^ Service workers

^ Cache Storage

^ this was helpful when testing and verifying if my SW was working

---
![html5_offline_storage.png fit](offline-first-diagram.png)

^ give a litte background on SW

^ act as a bridge
^ the application
^ the browser
^ the network

^ great for offline apps

^ they intercept network requests and take appropriate action based on whether the network is available

^ SW can create and manipulate cached requests

^ if the application is offline the service worker will handle responding to cached requests so they application wont error or even know the request failed

---
[.build-lists]

- Install
- Activate
- Message

^ the way to interact with a SW from a client is events

^ SW support 3 events we can use. Install, activate, and message

^ there are a other functional event types but those arent useful in my case

---

- caches.open('name')

- cache.put(url, response)
- cache.addAll([url,url,url])


^ SW caches and cache instance methods I will be using through out the code

^ cahces.open, a promise that resolves to the cache instance matching the given name

^ put, Takes a request and its response and adds it to the given cache

^ addAll, Takes an array of URLs, retrieves them, and adds the responses to the given cache

---

![chrome offline](chrome_offline.gif)

^ before I could think about offline audio

^ had to make my application work offline

^ this tripped me up a few times, it wasnt intuitive at first that my application wouldnt run offline

^ i always understood that browsers would naturally cache things for use when not available

^ while this might be true it doesnt exactly work that way

^ I needed to use the service worker to cache all my resources so my application would work offline

^ otherwise all anyone would ever see is this * point to screen *

---

![caching app assets fit](caching_application_assets.png)

^ listen for an "Install" event

^ get the cache instance

^ use cache.addAll to cache a list of all the application resources

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 1-7]
[.code-highlight: 9,14]
[.code-highlight: 11]
[.code-highlight: 12]

```js
var urlsToCache = [
    '/scripts.js',
    '/service-worker.js',
    '/sdk.html',
    '/marx.css',
    '/findaway-sdk.2.3.0-alpha20190319.modern.js'
];

self.addEventListener('install', event => {
    event.waitUntil(
        caches.open('my-site-cache-v1')
            .then((cache) => cache.addAll(urlsToCache))
    );
});

```

^ first I had to cache all my applicaiton assets

^ includes

^ HTML, CSS, JS, Images, and CDN resources

^ anything needed to display the application initally

^ i created a list of all the apllication assets I want cached

^ i listen for the install event mentioned before, this gets called when you register a service worker in your app

^ once I get that event

^ get cache instance with caches.open

^ use the cache instance cache.addAll to cache the list of resources

---

![claim sw clients fit](claim_serviceworker_clients.png)

^ to interact between client and sw

^ clients need to be claimed

^ this can be thougth of as connecting the SW to the clients

^ when the SW gets an activate message

^ use the clients.claim method to claim all windows or tabs using the SW

^ if we dont do this clients wont be able to talk to the SW

^ the SW wont know about the clients

^ Updates, and messages getting sent wont update the interface

---
[.background-color: #eeeeee]
[.text: #222222]

```js
self.addEventListener('activate', event => {
    self.clients.claim();
});
```

^ pretty simple

^ when we get the SW activate event

^ use clients.claim to connect

---
[.background-color: #eeeeee]
[.text: #222222]

```js
if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('service-worker.js');
}
```

^ here is how I registered the SW in my application

^ this will trigger the "install" event in the SW

^ the SW js file must be local to the application

^ in the root folder of the website

^ keep in mind you can only register one SW at a time

^ each one will kick out the last

---

![synergy fit](synergy.gif)

^ now had a service worker registered, I needed a way to interact with it from the SDK

---

![cross communication fit](cross_communication.png)

^ I need a way to talk between SW and SDK

^ use the message event from before

^ post message and event listeners

---
[.background-color: #eeeeee]
[.text: #222222]

```js
self.addEventListener('message', async (event) => {
    let client = event.ports[0];

    try {
        // do something with data in message
        client.postMessage(/*Send something back to the client*/);
    } catch (e) {
        client.postMessage(/*Send an Error back to the client*/);
    }
});
```

^ in my SW this meant adding an event listener for message events

^ any time the SW gets a message it does something with the data in the message and posts a message back to the client for either success or failure

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 3]
[.code-highlight: 4-6]
[.code-highlight: 8-11]

```js
SDK.sendMessage = (type, data) => (
    new Promise((resolve, reject) => {
        let messageChannel = new window.MessageChannel();
        messageChannel.port1.onmessage = (event) => {
            // Do something with event infomration from our ServiceWorker
        };

        navigator.serviceWorker.controller.postMessage(
            {type, data},
            [messageChannel.port2]
        );
    });
);
```

^ in my SDK I needed a way to send and recieve messages

^ that looks something like this.

^ create message channel

^ two scripts attached to the same document to communicate

^ this is why claiming the clients in the SW is so important

^ assign an on message function

^ this will allow me to get responses back from the SW

^ send a message using post message

^ i could send anything in the message body I wanted

^ I choose to send an object with a few properites to describe what the intent of the message was

^ this of this like a reducer and dispatch function

^ sent type which is the action I want the SW to take, like cache url, cache audio, or clear cache

^ and data which is any data associated with the action

^ tell post message to use port 2 on the message channel

---

## Demo

^ lets take a look at this working in chrome and the code used

---

![take a message fit](takeamessage.gif)

^ once I could talk between the two i needed to figure out how to best cache everything we need

^ This was easy for somethings but harder for others

---

![fetch fit](fetch.gif)

^ For our basic API requests I used fetch

^ this allowed me to make a request, from within my SW

^ to cache requests I used SW `cache.put` method wich allows me to store request and response pairs

---

![cache request fit](cache_request.png)

^ in my function to cache URLs

^ first open a Cache

^ then make a fetch request for the URL

^ when it gets a response use cache.put to add it to the SDK cache

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 2, 3, 6]
[.code-highlight: 4]
[.code-highlight: 5]

```js
const cacheURL = ({url, headers}) => {
    return caches.open(`SDK-cache`)
        .then(cache => {
            fetch(url, {headers: new Headers(headers)})
                .then(resp => cache.put(url, resp))
        });
};
```

^ here is what that code looks like in my SW

^ use caches.open to get a cache instance

^ fetch the url

^ I had to include the request headers so the SW could make requests with the API key

^ when successful put the url and response into the cache

^ pretty straight forward

---

![cache Audio fit](cache_audio.png)

^ to cache audio is simmilar

^ open up the cache

^ then use cache.addAll to add the urls to the cache

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 2]
[.code-highlight: 3]

```js
const cacheAudio = ({id, urls}) => {
    return caches.open(`book-${id}`)
        .then((cache) => cache.addAll(urls))
};
```

^ here is the code

^ open up the cache, using a unique audiobook ID passed from the SDK

^ and use cache.addAll to update the cache

^ easy right?

^ well...

---

![save](save.gif)

^ after getting feedback on my inital beta

^ I learned that I needed to provide some way of knowing how much of the audio cached.

^ This proved to be a bit harder, still not impossible but harder

---

![cache audio with status fit](cache_audio_with_status.png)

^ instead of using addAll

^ after opening the cache

^ i used forEach to loop over the urls

^ following the same path as the single urls

^ make a fetch request

^ use cache.put to add the url and response to the cache

^ and along the way send a message back to the client that the URL has been cached

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 2]
[.code-highlight: 4-8]
[.code-highlight: 9]

```js
urls.forEach(url => {
    fetch(url)
        .then(resp => {
            messageClient({
                type: 'AudioCached',
                id: id,
                url
            });
            return cache.put(url, resp);
        })
        .then(() => resolve(url))
        .catch(err => reject(err));
});
```

^ here you see just the forEach

^ for each url, make a fetch request

^ then message the client saying it has been cached

^ and use cache.put to add to the cache

---

![audio status events fit](audio_status_events.png)

^ in the SDK I needed to listen for those status events

^ Whenever the SDK recieves a message from the SW

^ if its not a cache status event, bail and return

^ if cache status event

^ add the url to the list of completed urls

^ calculate the percentage complete, based on the complete urls and playlist

^ trigger an event in our SDK to update the application

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 2]
[.code-highlight: 3]
[.code-highlight: 4]
[.code-highlight: 5]

```js
navigator.serviceWorker.addEventListener('message', (event) => {
    if (!event.data || event.data.type !== 'AudioCached') return;
    completeUrls.push(event.data.url);
    let percent = completeUrls.length / this.playlist.length;
    sdk.trigger(`cache:${this.id}`, percent);
});
```

^ here you see the event listener

^ if its not a cache status event, just bail

^ add the url to the list of completed

^ calculate our percentage

^ trigger an event to update the app

---

## DEMO

^ lets take a quick look at caching of urls

---

![winamp fit](winamp.png)

^ playlists

^ how we deliver MP3 urls

---
[.background-color: #eeeeee]
[.text: #222222]

```json
[
    {
        "url": "https://api.url.to/audio/0_0.mp3",
        "part_number": 0,
        "chapter_number": 0
    }
]
```

^ array of objects

^ url - path to the MP3 file

^ part_number/chapter_number - defines that tracks position in the playlist

^ this is how we know what auido to play and when to play it

^ delivered over POST not GET like everything else

---

![blocked fit](blocked.gif)

^ every project has issues

^ The major issue I had was when I would attempt to cache POST requests the service worker wouldnt cache the request, ever

^ after some research I learned that the service worker cache api does not support caching post request

---

![rage fit](rage.gif)

^ i was on the verge of throwing in the towel

^ change the API method?

^ API Developer said no

^ couldnt change in time to ship this feature

^ if I couldnt cache the playlists there is no point

^ I need playlists to make this whole thing work

^ no playlists mean no audio online or offline

^ does anyone have an idea of how to solve this?

---

![aha fit](aha.gif)

^ the lightbulb moment was when i realized I didnt need service workers for this

^ playlists are JSON

^ can stringify

^ I could use local storage!

^ remember that thing i said wouldnt work before...

---

![cache post fit](cache_post_request_.png)

^ in the SDK when we make a POST request

^ JSON stringify the response and add it to LS

---
[.background-color: #eeeeee]
[.text: #222222]

```js
if (method === 'POST') {
    // Cache with LocalStorage
    window.localStorage.setItem(url + '-' + method, JSON.stringify(response));
}
```

^ here is what that looks like

^ any time we make a POST request it gets cached in local storage instead of the service worker

^ I use a "url dash method" naming convention for

^ easy to lookup later in our SDK

^ its that simple

---

![automation fit](automation.gif)

^ once i had everything in place to cache urls I needed to automate the process

^ that meant any time the SDK made a successful API request it would cache the response

---

![turn-on fit](turnon.gif)

^ before we could do anything automatically

^ i had to make sure I wasnt introducing funcitonallity some of our partners might not want

^ My solution was to add a config option for the SDK to allow turning offline caching on or off

---
[.background-color: #eeeeee]
[.text: #222222]

```js
this.enable_cache = options.enable_cache && !!navigator.serviceWorker;
```

^ this is the code

^ cache is enabled if

^ its enabled in the options

^ and the browser supports SW

<!---

```js
this.onLine = (window && window.navigator && window.navigator.onLine) || true;

window.addEventListener('offline', () => {
    this.onLine = false;
    sdk.trigger('offline');
});
window.addEventListener('online', () => {
    this.onLine = true;
    sdk.trigger('online');
});
```

^ provide a flag on the SDK if online

^ allows anyone implementing to know without setup

^ I also provide some SDK events

^ apps can listen for those and update if needed
-->
---

![automatically caching fit](automatically_cache.png)

^ if caching is enabled

^ when the SDK gets a successful API response

^ if GET send a message to the SW to cache the request

^ if POST cache in LS

^ for errors

^ GET Requests will be resolved by the SW, magic!

^ if its a POST request get the cache from LS

---
[.background-color: #eeeeee]
[.text: #222222]

```js
const success = ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304);
```

^ success is any status

^ equal or greater than 200

^ less than 300

^ or 304 redirects

^ everything else is an error

---
[.background-color: #eeeeee]
[.text: #222222]

```js
if (success && enable_cache && method !== 'POST') {
    sendMessage('cacheAPI', {
        url: url,
        headers: cacheHeaders
    });
}
```

^ success

^ we have caching enabled

^ method is not POSt

^ send a message to the SW to cache the request and response

---
[.background-color: #eeeeee]
[.text: #222222]

```js
if (success && enable_cache && method === 'POST') {
    window.localStorage.setItem(url + '-' + method, JSON.stringify(response));
}
```

^ successful request

^ caching is enabled

^ method is POST

^ use LS to cache the response

^ use "url dash method" name

---

![SW Request fit](sw_request.png)

^ SW handles resolving offline GET requests

^ nothing I need to do

---
[.background-color: #eeeeee]
[.text: #222222]

```js
var lsCache = window.localStorage.getItem(url + '-' + method);
if (!success && method === 'POST' && enable_cache && lsCache) {
    resolve(JSON.parse(lsCache));
    return false;
}
```

^ error on request

^ lookup any data for the request in LS

^ if Method is POST

^ cache enabled

^ have data in LS

^ JSON parse the data

^ resolve the request as successful

---

![it crowd fit](itcrowd.gif)

^ I wanted to provide an intuitive API to allow our partners to support offline playback

---
[.build-lists]

- Cache Audio on Demand
- Check for cached book
- Clear cached book

^ cache audio when needed

^ cannot cache audio automatically

^ audio could be too large

^ check if a book is cached

^ can update application

^ clear book's cache

^ includes SW cache and LS

---

![sdk cache fit](SDK_cache.png)

^ for the cache on demand method

^ if cache is not enable, bail out to not break existing apps

^ setup a message event listener, form the status update example before

^ send a message to the SW

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 3]
[.code-highlight: 5-11]
[.code-highlight: 13-18]

```js
cache () {
    return new Promise((resolve, reject) => {
        if (!enable_cache) { return reject(new Error('Caching Not Enabled.')); }

        let cachedAudio = [];
        navigator.serviceWorker.addEventListener('message', (event) => {
            if (!event.data || event.data.type !== 'AudioCached') return;
            cachedAudio.push(event.data.url);
            let percent = cachedAudio.length / this.playlist.length;
            sdk.trigger(`cache:${this.id}`, percent);
        });

        return sendMessage('cacheAudio', {
                id: this.id,
                urls: this.playlist.map(p => p.url)
            })
            .then((resp) => resolve(resp))
            .catch(err => reject(err));
    });
}
```

^ to do caching on demand

^ if cache is not enabled bail out

^ creates the status updates event listenerh

^ send a message to cache the audiobook playlist

^ passing the audiobook ID and list of MP3 URLs

---

![check caches fit](check_caches.png)

^ before we can add an SDK method

^ I needed a way to check if items are cached in the SW

^ if checking a single url

^ open up the cache instance

^ use cache.match to lookup the URL

^ if success, then return true

^ if failure, then return false

^ if checking all caches

^ use caches.keys to get the keys for every cache

^ if success reutrn the list of keys

^ if error then return the error

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 3-8]
[.code-highlight: 4]
[.code-highlight: 5-7]
[.code-highlight: 9-11]

```js
const checkCaches = (data) => {
    return new Promise((resolve, reject) => {
        if (data.url) {
            return caches.open('demo-cache')
                .then(cache => cache.match(data.url))
                .then(() => resolve(true))
                .catch(err => reject(err));
        }
        return caches.keys()
            .then(keys => resolve(keys))
            .catch(err => reject(err));
    });
};
```

^ in the code

^ if checking a single url

^ use caches.has and return true or false

^ if checking all cache

^ use caches.keys and return the keys or an error

---
[.background-color: #eeeeee]
[.text: #222222]

```js
sendMessage('checkCaches')
    .then(keys => {
        isAudioCached = keys.includes(`book-${this.id}`);
    });
```

^ check if audio is cached

^ send message to SW

^ without a url, to get back a list of caches

^ if that succeeds

^ check if the list of keys includes the "book dash id"

^ audio cached if so

^ otherwise not

---
[.background-color: #eeeeee]
[.text: #222222]

```js
let url = urlFor('audiobooks', {id: this.id, account_id: this.account_id});
sendMessage('checkCaches', {url})
    .then(cached => {
        isMetadataCached = cached;
    });
```

^ check for cached metadata

^ get the metadata url

^ send a message to the SW to check cache for URL

^ if that succeeds

^ then our metadata is cached

---
[.background-color: #eeeeee]
[.text: #222222]

```js
sendMessage('checkCaches', {url: this.cover_url})
    .then(cached => {
        isCoverCached = cached;
    });
```

^ check for cached metadata

^ send a message to the SW to check cache for cover URL

^ if that succeeds

^ then our metadata is cached

---
[.background-color: #eeeeee]
[.text: #222222]

```js
let playlistUrl = urlFor('playlist', {id: this.id, account_id: this.account_id});
let isPlaylistCached = !!(window.localStorage.getItem(`${playlistUrl}-POST`));
```

^ check LS for playlist cache

^ get the Playlist URL

^ use localStorage.getItem to check for our cache

^ using the easy to remember "url dash method" name I created before

^ coerce the result into a boolean

^ either true if there is a value or false if not

---

![sdk check cache fit](SDK_check_cache.png)

^ in the SDK method

^ if cache is not enable bail

^ send some messages to the SW

^ based on the result of the messages

^ and the status of having the playlist in LS

^ send back an object that has true or false for the parts of the audiobook

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 3]
[.code-highlight: 7]
[.code-highlight: 10]
[.code-highlight: 12,13,16]
[.code-highlight: 17]
[.code-highlight: 20,21]
[.code-highlight: 22-28]

```js
checkCache () {
    return new Promise((resolve, reject) => {
        if (!enable_cache) { return reject(new Error('Caching Not Enabled.')); }
        let isAudioCached = false;
        let isMetadataCached = false;

        sendMessage('checkCaches')
            .then(keys => {
                if (keys.error) throw new Error(keys.error);
                isAudioCached = keys.includes(`book-${this.id}`);

                let url = urlFor('audiobooks', {id: this.id, account_id: this.account_id});
                return sendMessage('checkCaches', {url});
            })
            .then((cached) => {
                isMetadataCached = cached;
                return sendMessage('checkCaches', {url: this.cover_url});
            })
            .then((isCoverCached) => {
                let playlistUrl = urlFor('playlist', {id: this.id, account_id: this.account_id});
                let isPlaylistCached = !!(window.localStorage.getItem(`${playlistUrl}-POST`));

                resolve({
                    cover: isCoverCached,
                    metadata: isMetadataCached,
                    playlist: isPlaylistCached,
                    audio_media: isAudioCached
                });
            })
            .catch(err => reject(err));
    });
}
```

^ setup a function to check for cached data

^ like before if cache is disabled bail out

^ ask the service worker for the list of caches

^ check if the audio key is in the list of caches

^ send a message to check for the metadata url

^ send a message to check for the cover image url

^ check local storage if the playlist is cached

^ resolve with an object of the cache status for the Audiobook

---

![clear caches fit](clear_caches.png)

^ another fucntion I needed in my SW was clear cache

^ use caches.delete to delete the cache

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 3-5]

```js
const clearCaches = ({id}) => {
    return new Promise((resolve, reject) => {
        return caches.delete(`book-${id}`)
            .then(() => resolve(true))
            .catch(err => reject(err));
    });
};
```

^ in the SW code

^ for the ID passed in

^ use caches.delete to remove the cache "book-ID"

^ return true or an error

---

![sdk clear cache fit](SDK_clear_cache.png)

^ to clear our cache

^ if not enable bail

^ send a message to SW with the ID of the audiobook

^ remove Localstorage cache

^ return true or Error

---
[.background-color: #eeeeee]
[.text: #222222]
[.code-highlight: all]
[.code-highlight: 3]
[.code-highlight: 5]
[.code-highlight: 7-8]
[.code-highlight: 9]

```js
clearCache () {
    return new Promise((resolve, reject) => {
        if (!enable_cache) { return reject(new Error('Caching Not Enabled.')); }

        return sendMessage('clearCaches', {id: this.id})
            .then(() => {
                let playlistUrl = urlFor('playlist', {id: this.id, account_id: this.account_id});
                window.localStorage.removeItem(`${playlistUrl}-POST`);
                resolve(true);
            })
            .catch(err => reject(err));
    });
}
```

^ setup a function to clear cached data

^ bail if not enabled

^ send a message to the SW to clear cache for "audiobook ID"

^ clear the local storage cache

^ return true

---

## Demo

---

![install fit](install.gif)

^ before I could call this done

^ how do partners use my new fancy service worker?

^ they can only register one SW in their application

^ how can they use my service worker without mucking up their own?

^ well SW have a magic function called Import Scripts

---
[.background-color: #eeeeee]
[.text: #222222]

```js
importScripts('ae-service-worker.js');
```

^ this allows you to import any javascript into the SW and apply the SW scope to that script

^ so all I have to do is distribute my SDK specific SW to our partners

^ they can then import it into their application SW

^ they can leverage the communication between my SDK and SW

---

![celebrate](celebrate.gif)

^ Now my SDK supports offline playback.

^ Partners can start enabling offline playback for their users

^ we have one confirmed partner who will be using this in production, and hopefully more in the future

---

![not bad fit](not-bad.gif)

^ real quick a couple take-aways

^ like i said before

^ when i started, I wasnt even sure it was possible

^ Not only is it possible its not as hard as you would think

---

![Browser Logos](browser_logos.jpg)

^ browsers now days are amazing!

^ This could not have been done even 2-3 years ago

^ no browser support back then

^ while my application and SW were specific to chrome and chromebooks

---

![SW Browser Support fit](SWBrowserSupport.png)

^ works in all browsers that support SW and the Cache API

^ all major browsers

^ chrome, FF, Edge, and Safari

^ IE is not supported

^ apparently Opera mini not supported either

---

## Thanks

---

@camwardzala
camwardzala.com
