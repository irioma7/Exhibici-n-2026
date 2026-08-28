[sw.js](https://github.com/user-attachments/files/31558166/sw.js)
// Service Worker Irioma — v3
const CACHE = 'irioma-v3';

self.addEventListener('install', function(e){
  self.skipWaiting();
});

self.addEventListener('activate', function(e){
  e.waitUntil(
    caches.keys().then(function(keys){
      return Promise.all(
        keys.filter(function(k){ return k !== CACHE; })
            .map(function(k){ return caches.delete(k); })
      );
    }).then(function(){ return self.clients.claim(); })
  );
});

self.addEventListener('fetch', function(e){
  // Always fetch HTML fresh from network
  if(e.request.destination === 'document'){
    e.respondWith(
      fetch(e.request, {cache: 'no-store'})
        .catch(function(){ return caches.match(e.request); })
    );
  }
});
