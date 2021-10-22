<template>
  <div class="chat__profile">
    <img alt="user__img" class="profile__img" :src="person.avatarUrl" />
    <p class="chat__name">{{ person.customName }}</p>
    
  </div>
</template>

<script>
export default {
  props: {
    person: Object,
  },
};
</script>

<style>
</style>