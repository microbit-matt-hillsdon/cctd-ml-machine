<style>
  .arrowLeft {
    border-width: 10px;
    border-color: rgba(255, 255, 255, 0);
  }
</style>

<!--
  (c) 2023, Center for Computational Thinking and Design at Aarhus University and contributors
 
  SPDX-License-Identifier: MIT
 -->

<script lang="ts">
  import { onDestroy, onMount } from 'svelte';
  import type { Unsubscriber } from 'svelte/store';
  import { currentData } from '../../script/stores/mlStore';
  import { state } from '../../script/stores/uiStore';

  let unsubscribeFromLiveData: Unsubscriber | undefined = undefined;

  onMount(() => {
    unsubscribeFromLiveData = currentData.subscribe(data => {
      const dataInArray = [data.x, data.y, data.z];
      updateDimensionLabels(dataInArray);
    });
  });

  onDestroy(() => {
    unsubscribeFromLiveData?.();
  });

  let labels = [
    { label: 'x', arrowHeight: 0, labelHeight: 0, color: '#f9808e', id: 0 },
    { label: 'y', arrowHeight: 0, labelHeight: 0, color: '#80f98e', id: 1 },
    { label: 'z', arrowHeight: 0, labelHeight: 0, color: '#808ef9', id: 2 },
  ];

  function updateDimensionLabels(axes: number[]) {
    for (let i = 0; i < 3; i++) {
      labels[i].arrowHeight = (2.1 - axes[labels[i].id]) * 2.32;
    }
    fixOverlappingLabels();
  }

  function fixOverlappingLabels() {
    labels.sort((a, b) => {
      return a.arrowHeight - b.arrowHeight;
    });

    const height0 = labels[0].arrowHeight;
    const height1 = labels[1].arrowHeight;
    const height2 = labels[2].arrowHeight;

    const MAX_DISTANCE = 1.1;
    const maxDistanceBetweenAll = height2 - height0;

    // If all notes are too close
    if (maxDistanceBetweenAll < MAX_DISTANCE * 2) {
      // Find middle and place labels around them
      const middle = maxDistanceBetweenAll / 2 + height0;
      labels[0].labelHeight = middle - MAX_DISTANCE;
      labels[1].labelHeight = middle;
      labels[2].labelHeight = middle + MAX_DISTANCE;
      return;
    }

    labels[0].labelHeight = height0;
    labels[1].labelHeight = height1;
    labels[2].labelHeight = height2;

    // If a pair are too close.
    for (let i = 0; i < 2; i++) {
      const diff = labels[i + 1].labelHeight - labels[i].labelHeight;
      if (diff > MAX_DISTANCE) continue;

      // Find middle and place labels around middle
      const middle = diff / 2 + labels[i].labelHeight;
      labels[i + 1].labelHeight = middle + MAX_DISTANCE / 2;
      labels[i].labelHeight = middle - MAX_DISTANCE / 2;

      break; // Only one will be close to the other. Otherwise all were too close
    }
  }
</script>

{#if $state.isInputConnected}
  <div class="h-40 w-6 relative">
    {#each labels as dimension}
      <div
        class="absolute arrowLeft -m-3.5"
        style="transform: translateY({dimension.arrowHeight +
          0.75}rem) scale(1, 0.75); border-right-color: {dimension.color};" />
      <p
        class="absolute ml-3 text-xl"
        style="transform: translateY({dimension.labelHeight -
          0.5}rem); color: {dimension.color};">
        {dimension.label}
      </p>
    {/each}
  </div>
{/if}
