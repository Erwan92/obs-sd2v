<script>
  import { onMount } from 'svelte'
  import { obs, sendCommand } from './obs.js'

  let collections = []
  let currentCollection = ''

  onMount(async function () {
    const data = await sendCommand('GetSceneCollectionList')
    collections = data.sceneCollections || []
    currentCollection = data.currentSceneCollectionName || ''
  })

  obs.on('CurrentSceneCollectionChanged', async (data) => {
    console.log('CurrentSceneCollectionChanged', data.sceneCollectionName)
    currentCollection = data.sceneCollectionName || ''
    // Émettre manuellement de nouvelles scènes, puisque OBS ne les envoie pas lorsque la collection change
    obs.emit('SceneListChanged', await sendCommand('GetSceneList'))
  })
  obs.on('SceneCollectionListChanged', async (data) => {
    console.log('SceneCollectionListChanged', data.sceneCollections.length)
    collections = data.sceneCollections || []
  })

  async function setCurrentCollection (event) {
    sendCommand('SetCurrentSceneCollection', { sceneCollectionName: event.target.value })
  }
</script>

<div class="select" style="margin: 0 .5rem .5rem 0;">
  <select bind:value={currentCollection} title="Change Collection" on:change={setCurrentCollection}>
  {#each collections as collection}
    <option value={collection}>{collection}</option>
  {/each}
  </select>
</div>
