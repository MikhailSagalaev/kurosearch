<script>
  import { createEventDispatcher } from 'svelte'
  import { postObserver } from '../postObserver'

  /**
   * @typedef {import("../../../../posts/Post").Post} Post
   */

  /** @type {Post} */
  export let post

  const dispatch = createEventDispatcher()

  /** @type {HTMLElement}*/
  let media
  $: {
    if (media) {
      postObserver.observe(media)
    }
  }
</script>

<img
  loading="lazy"
  data-src={post.sample_url}
  alt={post.id.toString()}
  style={`aspect-ratio: ${post.width} / ${post.height}`}
  width={post.width}
  height={post.height}
  bind:this={media}
  on:click={() => dispatch('click')}
/>

<style>
  img {
    display: block;
    width: 100%;
    height: 100%;
    object-fit: contain;
    contain: strict;
  }
</style>
