# better-svelte-virtual-list

Proper opinionated implementation of a virtual scrolling list for equal-height items in Svelte.

Demo (copied and pasted `App.svelte` and `VirtualList.svelte`): https://svelte.dev/repl/cbf7977674654d048c4a0fa8fcc1024b?version=3.38.3

This is not a general purpose virtual list that you `npm install` but rather a starting point for your own project. I believe every project has different requirements and there is no point in creating a virtual list component that tries to cater for every single use case. It leads to bloated code and bugs.

It was extracted from a code base of mine (_insert project link once it has a website_) and powers an interactive grid that easily renders a million rows in an instant.

https://user-images.githubusercontent.com/679144/126451893-af9a457a-848e-4d94-a38a-9c7178043bb0.mp4

(this video was recorded with a dev build and with open dev tools, so it could even be smoother)

By proper I mean:

- it uses `requestAnimationFrame` instead of `scroll` event
- it uses native scrolling, the actual moving / scrolling of items is done by the browser
- it intelligently re-uses items by _not_ using keyed each
- it is fully reactive and 99% declarative, I despise imperative code

Have fun.

I'll leave adding a `buffer` functionality (rendering items above and below the fold) to the reader. I didn't need it (Electron only).
