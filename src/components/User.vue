<template>
  <div
    class="user"
    :class="isSelected ? 'active' : ''"
    @click="$emit('selected', person.id)"
    :key="person.id"
  >
    <img :src="person.avatarUrl" alt="user" class="user__img" />
    <div class="user__info">
      <p class="user__name">{{ person.customName }}</p>
      <p class="user__massege">
        {{
          lastMessage.content.length > 30
            ? lastMessage.content.substring(0, 30) + "..."
            : lastMessage.content
        }}
      </p>
    </div>
    <div class="time">{{ lastMessage.time }}</div>
  </div>
</template>

<script>
export default {
  emits: ["selected"],
  props: {
    person: Object,
    isSelected: Boolean,
    lastMessage: Object,
  },
};
</script>