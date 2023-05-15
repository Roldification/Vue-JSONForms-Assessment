<template>
    <div>
        <span>custom gud ni</span>
        <input v-bind:value="control.data"/>
        <div class="error" v-if="control.errors">{{ control.errors }}</div>
    </div>
</template>

<script lang="ts">
import { type ControlElement, isStringControl,
  type JsonFormsRendererRegistryEntry,
  rankWith, } from '@jsonforms/core';
import { defineComponent, type RendererOptions } from 'vue';
import { rendererProps, useJsonFormsControl, type RendererProps } from '@jsonforms/vue';

const controlRenderer = defineComponent({
  name: 'StringControlRenderer',
  props: {
    ...rendererProps<ControlElement>(),
  },
  setup(props: RendererProps<ControlElement>) {
    console.log('i was read...')
    return useJsonFormsControl(props);
  },
  methods: {
    onChange(event: Event) {
      this.handleChange(
        this.control.path,
        (event.target as HTMLInputElement).value
      );
    },
  },
});

export default controlRenderer;

export const entry: JsonFormsRendererRegistryEntry = {
  renderer: controlRenderer,
  tester: rankWith(0, isStringControl),
};
</script>