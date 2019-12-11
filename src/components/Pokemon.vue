<template>
  <div class="column is-12-mobile is-4-desktop is-4-tablet">
    <div class="card">
      <div class="card-content">
        <h3 class="title is-size-4">{{index}} {{poke.name}}</h3>
        <h4 class="title has-text-link is-size-6">
          <span @click="showMore(poke.name);">{{poke.url}}</span>
        </h4>
        <button class="button is-small is-primary is-rounded" @click="showMore(poke.name);">ver mas</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["poke", "index"],
  methods: {
    showMore(id) {
      this.$emit("showModal", id);
    },
    mouseOver(id) {
      this.$emit("showModal", id);
    }
  }
};
</script>
