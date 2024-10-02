# Service Worker - cache fetch events

To ensure your app fetches new data while online but still caches the result for offline use, modify the service worker's `fetch` event to always try fetching fresh data from the network first.
If the network is unavailable, the app will fall back to the cached response. This approach is known as a "network-first" strategy.

Here’s how you can modify your service worker to implement this:

```javascript
// Define cache names
const CACHE_NAME = 'my-app-cache-v1';
const DYNAMIC_CACHE_NAME = 'my-app-dynamic-cache-v1';

// Files to cache during install
const STATIC_ASSETS = [
  '/', 
  '/index.html',
  '/styles.css',
  '/app.js',
  '/favicon.ico',
  // Add other static files you want to cache
];

// Install event: Cache static assets
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME).then(cache => {
      console.log('Opened cache');
      return cache.addAll(STATIC_ASSETS);
    })
  );
});

// Activate event: Clean up old caches
self.addEventListener('activate', event => {
  event.waitUntil(
    caches.keys().then(cacheNames => {
      return Promise.all(
        cacheNames.map(cache => {
          if (cache !== CACHE_NAME && cache !== DYNAMIC_CACHE_NAME) {
            console.log('Deleting old cache:', cache);
            return caches.delete(cache);
          }
        })
      );
    })
  );
});

// Fetch event: Network-first strategy
self.addEventListener('fetch', event => {
  event.respondWith(
    fetch(event.request)
      .then(networkResponse => {
        // If network fetch is successful, cache the response
        return caches.open(DYNAMIC_CACHE_NAME).then(cache => {
          cache.put(event.request, networkResponse.clone());
          return networkResponse;
        });
      })
      .catch(() => {
        // If network fetch fails, fallback to cache
        return caches.match(event.request);
      })
  );
});
```

### How it works:

1. **Network-first strategy**:
   - The `fetch` event tries to fetch the latest data from the network first (`fetch(event.request)`).
   - If the fetch is successful, the response is cached dynamically using `cache.put()`, and the fresh network response is returned to the app.
   - If the network request fails (e.g., offline mode), it falls back to the cached response (`caches.match(event.request)`).

### Benefits:
- **Fresh Data**: Your app will always attempt to load fresh content when online.
- **Offline Fallback**: If the network is unavailable, the cached version will still be served to ensure functionality. 

This ensures the app benefits from up-to-date data while online but still works when offline.

# Testing Your Service Worker and Cache

To check if your app is caching correctly using Chrome DevTools, you can follow these steps:

### 1. **Open Chrome DevTools**
   - Right-click on the page and select **Inspect** or press `Ctrl+Shift+I` (Windows/Linux) or `Cmd+Opt+I` (Mac) to open the DevTools.
   - Switch to the **Application** tab.

### 2. **Inspect Service Workers**
   - In the **Application** tab, look under the **Service Workers** section in the left sidebar.
   - Here, you can see the registered service worker for your app.
     - You should see whether the service worker is **active** and **controlling** the page.
     - You can use the "Update" button to force an update of the service worker.
     - Enable the **Offline** checkbox to simulate being offline.

### 3. **Check Cached Files (Cache Storage)**
   - In the **Application** tab, under **Cache Storage**, expand the cache list (you should see your cache names, such as `my-app-cache-v1` or `my-app-dynamic-cache-v1`).
   - When you click on one of the caches, you'll see a list of cached files on the right-hand panel.
     - Check if the assets or API responses that should be cached are listed here.
     - Click on any cached file to view its details (response headers, content, etc.).

### 4. **Test Fetching Behavior (Network Tab)**
   - Switch to the **Network** tab to monitor the fetch behavior.
     - Refresh the page while the DevTools are open.
     - Watch the list of network requests as they load.
     - Look at the **"Status"** and **"Size"** columns:
       - Files served from the cache will have a **"200 (from ServiceWorker)"** status.
       - If the file is fetched from the network, the status will be a regular **200**.
   - You can also hover over the **Initiator** column, and it will show **ServiceWorker** if the response came from the service worker.

### 5. **Simulate Offline Mode**
   - In the **Network** tab, set the network to **Offline** using the **Throttling** dropdown.
     - Reload the page while offline to see if your app still works and serves cached assets.
     - If the page loads properly and assets (like images, CSS, or API responses) are available, it means the cache is working correctly for offline use.

### 6. **Clear Cache or Service Workers**
   - In the **Application** tab, under **Storage**, you can clear the cache or unregister the service worker if needed.
     - Unregister the service worker and clear cache/storage to reset the app's caching and verify that your service worker and cache are updated properly when changes are made.

### Indicators that caching is working correctly:
- Your service worker should be **active** and **controlling** the page (visible in the Service Worker section).
- Cached files should appear under **Cache Storage** in the correct cache.
- In the **Network** tab, requests served from cache should show **"200 (from ServiceWorker)"**.
- Your app should load while **offline**, and previously fetched resources (like images, styles, or API data) should still be available. 

By combining these tools, you can verify whether your caching is functioning as expected and troubleshoot any issues.
