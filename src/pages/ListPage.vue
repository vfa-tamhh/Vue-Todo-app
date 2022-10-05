<template>
  <q-responsive :ratio="16/9">
    <q-page class="row justify-evenly">
      <list-component></list-component>
    </q-page>
  </q-responsive>
  
  
</template>

<script lang="ts">
import { Todo, Meta } from 'components/models';
import { defineComponent, ref } from 'vue'
import ListComponent from '../components/ListComponent.vue'

export default defineComponent({
    name: "ListPage",
    components: { ListComponent }
});
</script>
