<template>
    <MapboxMap
    ref="mapboxMap"
    access-token="pk.eyJ1IjoiZGV2YW5zaDM2NSIsImEiOiJjbDV5YnV2dW0wY2VlM2RwMjYzZzY1anFyIn0.5njP9f5rkA5KNHRmlALP_A" />
</template>

<script>
  import { ref } from 'vue';
  import { MapboxMap } from '@studiometa/vue-mapbox-gl';

  const mapboxMap = ref();
  const map = computed(() => mapboxMap.value.map);

</script>
