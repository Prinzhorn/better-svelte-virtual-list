# better-svelte-virtual-list

Proper opinionated implementation of a virtual scrolling list for equal-height items in Svelte.

This is not a general purpose virtual list that you `npm install` but rather a starting point for your own project. I believe every project has different requirements and there is no point in creating a virtual list component that tries to cater for every single use case. It leads to bloated code and bugs.

By proper I mean:

- it uses `requestAnimationFrame` instead of `scroll` event
- it uses native scrolling, the actual moving / scrolling of items is done by the browser
- it intelligently re-uses items by _not_ using keyed each
- it is fully reactive and 99% declarative, I don't like imperative code

Have fun.
