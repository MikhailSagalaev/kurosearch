<script>
  import { createEventDispatcher } from 'svelte'
  import onEnterOrSpace from '../common/onEnterOrSpace'
  import SearchableTag from './SearchableTag.svelte'

  /** @type {import("../../tags/Supertag").Supertag} */
  export let supertag

  const dispatch = createEventDispatcher()
  const emitRemove = () => dispatch('remove', supertag)
  const emitEdit = () => dispatch('edit', supertag)
</script>

<li>
  <h3>{supertag.name}</h3>
  <small>{Object.keys(supertag.tags).length} tags</small>
  <i tabindex="0" class="codicon codicon-edit" on:click={emitEdit} on:keyup={onEnterOrSpace(emitEdit)} />
  <i tabindex="0" class="codicon codicon-close" on:click={emitRemove} on:keyup={onEnterOrSpace(emitRemove)} />
  <span>{supertag.description}</span>
  <ol>
    {#each supertag.tags as tag}
      <SearchableTag {tag} />
    {/each}
  </ol>
</li>

<style>
  li {
    display: grid;
    grid-template-columns: auto 1fr auto auto;
    gap: 0.5rem;
    align-items: center;
    padding: 0.5rem;
    border-radius: var(--border-radius);
    transition: color var(--default-transition-behaviour);
  }

  h3 {
    font-weight: normal;
    font-size: var(--text-size-large);
    color: var(--text-highlight);
  }

  small {
    font-size: var(--text-size-small);
  }

  span {
    grid-area: 2/1/3/4;
  }

  ol {
    grid-area: 3/1/4/4;
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
  }

  i {
    height: 24px;
    width: 24px;
    line-height: 24px;
    text-align: center;
    border-radius: 12px;
  }

  @media (hover: hover) {
    i:hover {
      color: var(--text-highlight);
    }
  }
</style>
