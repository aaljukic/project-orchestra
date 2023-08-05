<template>
  <div class="role-card">
    <div class="role-content">
      <div class="role-header">
        <div class="role">
          <p class="role-name">{{ role.name }}</p>
          <p class="role-info">{{ role.type }}</p>
        </div>
        <div v-if="!role.active" class="acitve-tag">Inactive</div>
      </div>
      <p class="role-description">{{ role.description }}</p>
      <div class="user-list">
        <div class="user-image-container" v-for="user in role.users" :key="user.id">
          <img class="user-image" :src="user.photo_url" alt="" />
        </div>
      </div>
    </div>
    <div class="role-footer">
      <div class="date">{{ formatDate(role.created_on) }}</div>
      <div class="actions">
        <Icon v-if="!role.editable" id="icon" name="ion:ios-lock" color="white" size="20" />
        <div v-else class="buttons">
          <button @click="editRole" class="normal">EDIT</button>
          <button @click="deleteRole" class="danger">DELETE</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, defineProps } from 'vue';

const { role } = defineProps({
  role: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(['edit', 'delete']);

const editRole = () => {
  emit('edit', role);
};

const deleteRole = () => {
  emit('delete', role);
};

const formatDate = (dateString) => {
  const date = new Date(dateString);
  const day = String(date.getDate()).padStart(2, '0');
  const month = String(date.getMonth() + 1).padStart(2, '0');
  const year = date.getFullYear();
  return `Date created: ${month}/${day}/${year}`;
};
</script>

<style scoped lang="scss">
.role-card {
  width: 300px;
  background-color: white;
  border: solid 1px hsl(0, 1%, 73%);
  display: flex;
  flex-direction: column;

  .role-content {
    padding: 1rem;

  .role-header {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  
      .role {
        display: flex;
        flex-direction: column;
        margin-bottom: 1rem;

        .role-name {
          font-size: 18px;
          font-weight: 600;
          color: hsl(0, 0%, 32%);
        }

        .role-info {
          font-size: 14px;
          color: hsl(0, 0%, 48%);
        }

      }

      .acitve-tag {
        background-color: hsl(0, 91%, 61%);
        padding: 4px 8px;
        font-size: 13px;
        place-self: baseline;
        color: white;
        border-radius: 4px;
      }
  }
    .role-description {
      font-size: 14px;
      color: hsl(0, 0%, 48%);
      margin-bottom: 1rem;
    }

    .user-list {
      display: flex;
      flex-direction: row;
      overflow: auto;

      .user-image-container {
        width: 40px;
        height: 40px;
        border-radius: 50%;

        .user-image {
          width: 40px;
          height: 40px;
          border-radius: 50%;
        }
      }
    }

  }


  .role-footer {
    background-color: hsl(0, 0%, 78%);
    height: 50px;
    display: flex;
    flex-direction: row;
    place-items: center;
    padding: 1rem;
    justify-content: space-between;

    .date {
      font-size: 10px;
      color: hsl(0, 0%, 39%);
    }

    .actions {
      display: flex;
      flex-direction: row;

      button {
        cursor: pointer;
        padding: 4px 8px;
        border: none;
        background-color: transparent;

        &:last-child {
          margin-left: 1rem;
        }
      }

      .danger {
        color: red;
      }
      .normal {
        color: hsl(0, 0%, 13%);
      }
    }
  }

}</style>
