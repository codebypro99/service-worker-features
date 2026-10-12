# Service Worker Feature Sample

A JavaScript service worker excerpt demonstrating on-demand caching for content-hashed assets and a navigation fallback when the application server cannot be reached.

**Source:** [sw.js](./features/sw.js)

## Purpose

This feature shows how I apply different request strategies to static assets and document navigation. Assets are cached as they are used, while navigation continues to request the current application document from the server.

## On-Demand Asset Caching

- Handles same-origin GET requests only.
- Matches filenames with a hash-like suffix for JavaScript, CSS, fonts, SVG, and PNG assets.
- Checks the named cache before fetching a matching asset.
- Stores a clone of a successful network response for subsequent requests.
- Uses no precache list, so assets are downloaded only when requested by the client.

**What this demonstrates:** implementing a focused cache-first strategy for build assets whose names change with their content.

## Navigation Fallback

- Uses a network request for eligible document navigation.
- Returns a small inline HTML page when that request rejects.
- Gives the fallback response HTTP status 503 and an HTML content type.
- Displays a retry link and an application-specific explanation of server connectivity.

**What this demonstrates:** providing a readable connectivity failure state while retaining network navigation and server redirects.

## Request Boundaries

The handler excludes cross-origin requests, non-GET requests, paths beginning with `/api/`, the exact `/login` path, and the worker's own `/sw.js` path. Other requests that match neither the hashed-asset rule nor navigation mode are left to normal browser handling.

## Worker Lifecycle

- Calls `skipWaiting()` during installation.
- Deletes cache names other than the current cache during activation.
- Claims clients after activation cleanup completes.

**What this demonstrates:** making worker activation and cache cleanup explicit rather than leaving them implicit in request handling.

## Technologies

JavaScript, browser Service Worker events, Cache Storage, Fetch, URL parsing, and inline HTML responses.

## Excerpt Context and Behavior Boundaries

Worker registration, deployment scope, asset build configuration, and the application server belong to the surrounding project. Original source code, cache names, and application-specific fallback text are preserved.

Activation removes every other cache name visible in the origin's Cache Storage, without restricting cleanup to this worker's own naming prefix. The filename pattern assumes hash-like suffixes represent immutable content; it does not verify that assumption.

Cache writes are initiated without awaiting completion or extending event lifetime through `waitUntil()`. Cache quota management and explicit handling of cache-write errors are outside the excerpt.

The navigation fallback handles rejected fetches, not ordinary HTTP error responses. The fallback text states that server-side terminals remain running, but the worker does not check terminal state. Cached assets alone do not provide offline application functionality.

The description is based on source inspection. This feature sample has not been registered or exercised in a browser as part of this collection.

## Portfolio Message

I can implement focused browser request strategies that reduce repeated asset downloads and provide clear navigation feedback during connectivity failures.
