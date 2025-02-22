<script>
  import { getPage } from '../../api-client/ApiClient'
  import { ActiveTag as AT } from '../../tags/ActiveTag'

  import userdata from '../account/userdata'
  import Button from '../common/Button.svelte'
  import CreateSupertagDialog from '../supertags/CreateSupertagDialog.svelte'
  import TagInput from '../tag-input/TagInput.svelte'

  import activeTags from './activeTagsStore'
  import Results from './Results.svelte'
  import results from './resultsStore'
  import ScrollUpButton from './ScrollUpButton.svelte'
  import SearchError from './SearchError.svelte'
  import sortStore from './sortStore'
  import Title from './Title.svelte'
  import ActiveTagList from './ActiveTagList.svelte'


  let supertagMode = false
  let error = undefined

  const getFirstPage = async () => {
    results.reset()
    return getNextPage()
  }

  const getNextPage = async () => {
    error = undefined

    try {
      const tags = getSearchableTags()
      const page = await getPage($results.nextPage, tags, $sortStore.sortProperty, $sortStore.minScore)

      results.addPage(page)
    } catch (e) {
      error = e
      console.warn(e)
    }
  }

  const getSearchableTags = () => {
    return $activeTags.flatMap((t) =>
      t.type === 'supertag' ? $userdata.supertags.find((s) => s.name === t.name).tags : t.toSearchableTag()
    )
  }
</script>

<div class="search-config">
  <Title />
  <TagInput on:pick={(e) => activeTags.addOrReplace(e.detail)} />
  <ActiveTagList on:supertag={() => {
    supertagMode = true
  }}/>
  <div class="sort-row">
    <Button title="Click to search with active tags" text="Search" on:click={() => getFirstPage()} />
  </div>
</div>

{#if error !== undefined}
  {#if error.message === 'Failed to fetch'}
    <SearchError
      title="Connection Error"
      icon="debug-disconnect"
      message="Failed to connect to the server. Make sure you have a stable internet connection."
    />
  {:else}
    <SearchError title="Application Error" icon="error" message={error.message} />
  {/if}
{:else}
  <Results on:endreached={getNextPage} on:configchange={getFirstPage} />
{/if}

<ScrollUpButton />

{#if supertagMode}
  <CreateSupertagDialog
    tags={$activeTags}
    on:submit={(ev) => {
      userdata.addSupertag(ev.detail)
      activeTags.set([new AT('+', ev.detail.name, ev.detail.tags.length, 'supertag')])
    }}
    on:close={() => {
      supertagMode = false
    }}
  />
{/if}

<style>
  .search-config {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    padding-inline: 1rem;
  }

  .sort-row {
    display: flex;
    justify-content: center;
    gap: 1rem;
  }
</style>
