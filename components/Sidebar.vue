<template>
  <div class="sidebar">
    <h6 class="list-header">Name</h6>
    <b-list-group>
      <b-list-group-item
        v-for="profile in profiles"
        :key="profile.name"
        @click="$emit('onProfileClick', profile)"
        :class="checkIsSelected(profile) ? 'active-profile' : null"
      >
        <h6 class="list-item-title mb-0">{{ profile.name }}</h6>
        <p class="list-item-caption mb-0">{{ profile.value }} MWh</p>
      </b-list-group-item>
    </b-list-group>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  props: ['profiles', 'selectedProfile'],
  methods: {
    checkIsSelected(profile) {
      if (!this.selectedProfile) return false
      return this.selectedProfile.name == profile.name
    }
  }
})
</script>

<style lang="scss" scoped>
.sidebar {
  color: $c-text;
  padding-top: 16px;
  height: 100%;
  border-bottom: 8px solid $c-primary;

  .list-header {
    padding-left: 16px;
  }

  .list-group-item {
    background-color: #f1f1f1;
    border: none;
    border-top: 1px solid white;
    padding: 12px 16px;
    cursor: pointer;

    &.active-profile {
      background-color: $c-active;
    }

    &:last-child {
      border-bottom: 1px solid white;
    }
  }
}

@media screen and (min-width: 768px) {
  .sidebar {
    border-right: 5px solid $c-primary;
    border-bottom: none;
  }
}
</style>