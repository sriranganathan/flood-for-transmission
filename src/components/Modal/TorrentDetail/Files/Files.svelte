<script>
  import { torrentDetails } from '~helpers/stores';
  import {
    TRANSMISSION_COLUMN_DOWNLOAD_DIR,
    TRANSMISSION_COLUMN_FILES,
  } from '~helpers/constants/columns';

  import Select from '~components/Select';
  import ActionBarView from '~components/Modal/TorrentDetail/ActionBarView';
  import Folder from './Folder';
  import {
    getFolderStructure,
    getMainFolder,
  } from '~helpers/folderStructureHelper';

  let selectedFiles = [];
  const prioOptions = [
    { label: "Don't download", value: -2 },
    { label: 'Low', value: -1 },
    { label: 'Normal', value: 0 },
    { label: 'High', value: 1 },
  ];

  $: files = $torrentDetails[TRANSMISSION_COLUMN_FILES].toSorted((a, b) =>
    a.name.localeCompare(b.name)
  );
  $: structure = getFolderStructure(files);

  const handleSelectedFilePrioChange = (event) => {
    torrentDetails.setPriority($torrentDetails, selectedFiles, event.detail);
  };

  const handleSingleFilePrioChange = (fileIndex, event) => {
    torrentDetails.setPriority($torrentDetails, [fileIndex], event.detail);
  };
</script>

<div class="container">
  <ActionBarView items="{selectedFiles}" itemName="file" itemNamePlural="files">
    {#if files.length}
      <Folder
        structure="{structure}"
        bind:selectedFiles="{selectedFiles}"
        iconName="Disk"
        folderName="{getMainFolder(
          $torrentDetails[TRANSMISSION_COLUMN_DOWNLOAD_DIR],
          files[0]
        )}"
        collapsible="{false}"
        strong="{true}"
        onSingleFilePrioChange="{handleSingleFilePrioChange}"
      />
    {:else}
      <div class="empty">
        No files to show right now. Metadata is probably missing.
      </div>
    {/if}

    <div slot="actions" class="select-container">
      <Select
        options="{prioOptions}"
        placeholder="Set priority"
        on:change="{handleSelectedFilePrioChange}"
      />
    </div>
  </ActionBarView>
</div>

<style>
  .container {
    display: flex;
    flex-direction: column;
    height: 100%;
  }

  .empty {
    color: var(--color-modal-text);
    font-size: 14px;
  }

  .select-container {
    flex-grow: 1;
  }
</style>
