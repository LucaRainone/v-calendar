<template>
<b-table
  :data='props'>
  <template scope='props'>
    <b-table-column label='Name'>
      <span v-html='props.row.name'></span>
    </b-table-column>
    <b-table-column label='Description'>
      <span v-html='props.row.description'></span>
    </b-table-column>
    <b-table-column label='Parameters'>
      <span v-html='props.row.parameters'></span>
    </b-table-column>
  </template>
</b-table>
</template>

<script>
export default {
  data() {
    const props = [
      {
        name: '<code>update:fromPage</code>',
        description: 'Calendar left/single pane moved to a different page.',
        parameters: '<code>page: Object</code>',
      },
      {
        name: '<code>update:toPage</code>',
        description: 'Calendar right pane moved to a different page.',
        parameters: '<code>page: Object</code>',
      },
      {
        name: '<code>daySelect</code>',
        description: 'Calendar day cell was selected either by tap on touch devices or by mouse click.',
        parameters: '<code>dayInfo: Object</code>, <code>attributes: Object</code>',
      },
      {
        name: '<code>dayMouseEnter</code>',
        description: 'Mouse cursor entered a calendar day cell.',
        parameters: '<code>dayInfo: Object</code>, <code>attributes: Object</code>',
      },
      {
        name: '<code>dayMouseLeave</code>',
        description: 'Mouse cursor left a calendar day cell.',
        parameters: '<code>dayInfo: Object</code>, <code>attributes: Object</code>',
      },
    ];
    return {
      props,
    };
  },
};
</script>
