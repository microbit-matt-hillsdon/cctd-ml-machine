<!--
  (c) 2023, Center for Computational Thinking and Design at Aarhus University and contributors
 
  SPDX-License-Identifier: MIT
 -->

<script lang="ts">
  import { t } from '../../i18n';
  import TypingUtils from '../../script/TypingUtils';
  import { state } from '../../script/stores/uiStore';
  import StandardButton from '../StandardButton.svelte';

  export let onOutputDisconnectButtonClicked: () => void;
  export let onOutputConnectButtonClicked: () => void;
  export let onInputDisconnectButtonClicked: () => void;
</script>

<!-- These are the buttons that are present while the input micro:bit is connected-->
<div class="flex flex-row mr-4">
  {#if $state.isPredicting || $state.isTraining || $state.isOutputConnected}
    {#if $state.isOutputAssigned}
      <!-- Output is assigned -->
      {#if !$state.isOutputConnected || $state.isOutputReady}
        <!-- Output MB is not in the connection process -->
        <StandardButton onClick={onOutputDisconnectButtonClicked} color="warning"
          >{$t('menu.model.disconnect')}</StandardButton>
      {:else}
        <StandardButton onClick={TypingUtils.emptyFunction} color="disabled">
          <img alt="loading" src="imgs/loadingspinner.gif" style="height:24px" />
        </StandardButton>
      {/if}
    {:else}
      <StandardButton onClick={onOutputConnectButtonClicked}>
        {$t('menu.model.connectOutputButton')}
      </StandardButton>
    {/if}
  {/if}
  <div class="ml-2">
    {#if !$state.isInputConnected || $state.isInputReady}
      <!-- Input MB is not in the connection process -->
      <StandardButton onClick={onInputDisconnectButtonClicked} color="warning"
        >{$t('footer.disconnectButton')}</StandardButton>
    {:else}
      <StandardButton onClick={TypingUtils.emptyFunction} color="disabled">
        <img alt="loading" src="/imgs/loadingspinner.gif" style="height:24px" />
      </StandardButton>
    {/if}
  </div>
</div>
