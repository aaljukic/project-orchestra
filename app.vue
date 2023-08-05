<template>
  <div id="page">
    <h2>User Roles Management</h2>
    <div id="page-header">
      <div id="inputs">
        <input class="input" placeholder="Search" v-model="search" />
        <select class="input" v-model="activeInactiveRole">
          <option value="all">All</option>
          <option value="active">Active</option>
          <option value="inactive">Inactive</option>
        </select>
      </div>
      <button @click="openNewRoleModal">CREATE NEW ROLE</button>
    </div>
    <div id="card-list">
      <div v-for="role in filteredRoles" :key="role.id">
        <OrchestraCard @edit="openEditModal" @delete="openDeleteModal" :role="role" />
      </div>
    </div>
    <dialog v-show="isNewRoleModalOpen" id="new-role-dialog">
      <form @submit.prevent="saveNewRole">
        <label for="name">Name:</label>
        <input type="text" id="name" v-model="newRole.name" required />
        <label for="type">Type:</label>
        <input type="text" id="type" v-model="newRole.type" required />
        <label for="description">Description:</label>
        <textarea id="description" v-model="newRole.description" required></textarea>
        <label for="active">Active:</label>
        <input type="checkbox" id="active" v-model="newRole.active" />
        <label for="editable">Editable:</label>
        <input type="checkbox" id="editable" v-model="newRole.editable" />
        <div v-for="(user, index) in newRole.users" :key="index">
          <label :for="'firstName-' + index">First Name:</label>
          <input type="text" :id="'firstName-' + index" v-model="user.first_name" required />
          <label :for="'lastName-' + index">Last Name:</label>
          <input type="text" :id="'lastName-' + index" v-model="user.last_name" required />
          <label :for="'photoUrl-' + index">Photo URL:</label>
          <input type="text" :id="'photoUrl-' + index" v-model="user.photo_url" />
          <button @click="removeUser(index)">Remove User</button>
        </div>
        <button class="add-new-item" @click="addUser">Add User</button>
        <button class="confirm" type="submit">Save</button>
        <button class="cancel" @click="closeNewRoleModal">Cancel</button>
      </form>
    </dialog>
    <dialog v-show="isEditRoleModalOpen" id="edit-role-dialog">
      <form @submit.prevent="editRole">
        <label for="name">Name:</label>
        <input type="text" id="name" v-model="selectedRole.name" required />
        <label for="type">Type:</label>
        <input type="text" id="type" v-model="selectedRole.type" required />
        <label for="description">Description:</label>
        <textarea id="description" v-model="selectedRole.description" required></textarea>
        <label for="active">Active:</label>
        <input type="checkbox" id="active" v-model="selectedRole.active" />
        <label for="editable">Editable:</label>
        <input type="checkbox" id="editable" v-model="selectedRole.editable" />
        <div v-for="(user, index) in selectedRole.users" :key="user.id">
          <label :for="'firstName-' + index">First Name:</label>
          <input disabled :id="'firstName-' + index" v-model="user.first_name" />
          <label :for="'lastName-' + index">Last Name:</label>
          <input disabled :id="'lastName-' + index" v-model="user.last_name" />
        </div>
        <button class="confirm" type="submit">SAVE CHANGES</button>
        <button class="cancel" @click="closeEditModal">Cancel</button>
      </form>
    </dialog>
    <dialog v-show="isDeleteRoleModalOpen" id="delete-role-dialog">
      <p>are you sure you want to delete a role</p>
      <button @click="deleteRole" class="danger">DELETE</button>
      <button class="cancel" @click="closeDeleteModal">Cancel</button>
    </dialog>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const roles = ref([]);
const isNewRoleModalOpen = ref(false);
const isEditRoleModalOpen = ref(false);
const isDeleteRoleModalOpen = ref(false);
const search = ref('');
const activeInactiveRole = ref('all');
const initRole = {
  id: 0,
  name: '',
  type: '',
  description: "Lorem ipsum dolor sit amet",
  editable: true,
  active: true,
  users: [],
  created_on: "2019-01-18T18:25:43.511Z",
  modified_on: "2019-01-18T18:25:43.511Z"
}
const newRole = ref(initRole);
const selectedRole = ref(initRole);

// new role
const openNewRoleModal = () => {
  isNewRoleModalOpen.value = true;
  newRole.value = { ...initRole };
  selectedRole.value = { ...initRole };
  const dialog = document.getElementById('new-role-dialog');
  dialog.showModal();
};

const closeNewRoleModal = () => {
  isNewRoleModalOpen.value = false;
  newRole.value = { ...initRole };
  selectedRole.value = { ...initRole };
  const dialog = document.getElementById('new-role-dialog');
  dialog.close();
};


const saveNewRole = () => {
  newRole.value.id = roles.value.length + 1;
  newRole.value.created_on = new Date().toISOString();
  newRole.value.modified_on = new Date().toISOString();
  roles.value.push({ ...newRole.value });
  closeNewRoleModal();
};

const addUser = () => {
  newRole.value.users.push({
    id: newRole.value.users.length + 1,
    first_name: '',
    last_name: '',
    photo_url: "http://placekitten.com/60/60",
  });
};

const removeUser = (index) => {
  newRole.value.users.splice(index, 1);
};

// edit modal
const openEditModal = (role) => {
  isEditRoleModalOpen.value = true;
  selectedRole.value = { ...role };
  const dialog = document.getElementById('edit-role-dialog');
  dialog.showModal();
};

const closeEditModal = () => {
  isEditRoleModalOpen.value = false;
  const dialog = document.getElementById('edit-role-dialog');
  dialog.close();
};

const editRole = () => {
  const index = roles.value.findIndex((role) => role.id === selectedRole.value.id);

  if (index > -1) {
    roles.value[index].name = selectedRole.value.name;
    roles.value[index].type = selectedRole.value.type;
    roles.value[index].active = selectedRole.value.active;
    roles.value[index].editable = selectedRole.value.editable;
    roles.value[index].description = selectedRole.value.description;
  }

  closeEditModal();
};



// delete modal
const openDeleteModal = (role) => {
  isDeleteRoleModalOpen.value = true;
  console.log("DELETE");
  selectedRole.value = { ...role };
  const dialog = document.getElementById('delete-role-dialog');
  dialog.showModal();
};

const closeDeleteModal = () => {
  isDeleteRoleModalOpen.value = false;
  const dialog = document.getElementById('delete-role-dialog');
  dialog.close();
};

const deleteRole = () => {
  const index = roles.value.findIndex((role) => role.id === selectedRole.value.id);
  if (index > -1) {
    roles.value.splice(index, 1);
  }
  closeDeleteModal();
};

const filteredRoles = computed(() => {
  return roles.value
    .filter((role) => {
      const nameAndTypeMatch =
        role.name.toLowerCase().includes(search.value.toLowerCase()) ||
        role.type.toLowerCase().includes(search.value.toLowerCase());
      if (activeInactiveRole.value === 'all') return nameAndTypeMatch;
      return (
        nameAndTypeMatch &&
        (activeInactiveRole.value === 'active' ? role.active : !role.active)
      );
    });
});

onMounted(async () => {
  const response = await fetch('/user_roles.json');
  roles.value = await response.json();
});
</script>


<style lang="scss" scoped>
#page {
  padding: 1rem;

  h2 {
    margin-bottom: 2rem;
    font-size: 22px;
    color: hsl(0, 0%, 50%);
  }

  #page-header {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    gap: 1rem;

    #inputs {
      display: flex;
      flex-direction: row;
      gap: 1rem;
      flex: 2;
      justify-content: space-around;

      .input {
        height: 30px;
        width: 100%;
        outline: 0;
        border-width: 0 0 2px;
        border-color: hsl(201, 100%, 50%);
        transition: all ease-in-out 300ms;
      }

      .input:focus {
        border-color: hsl(201, 100%, 63%);
      }
    }

    button {
      width: 150px;
      border: none;
      padding: 4px 8px;
      background-color: hsl(201, 100%, 63%);
      color: white;
      border-radius: 4px;

      &:active {
        background-color: hsl(201, 80%, 67%);
      }
    }
  }

  #card-list {
    display: grid;
    grid-gap: 1rem;
    margin-top: 2rem;
    grid-template-columns: repeat(1, 1fr);
  }

  @media (min-width: 600px) {
    #card-list {
      grid-template-columns: repeat(2, 1fr);
    }
  }

  @media (min-width: 900px) {
    #card-list {
      grid-template-columns: repeat(3, 1fr);
    }
  }

  dialog {
    width: 50%;
    height: 50%;
    display: flex;
    flex-direction: column;

    form {
      height: 100%;
      display: flex;
      flex-direction: column;
      gap: 1rem;
      overflow-y: auto;

      label {
        font-size: 12px;
        font-weight: 600;
      }

      input {
        height: 30px;
        width: 100%;
        outline: 0;
        border-width: 0 0 2px;
        border-color: hsl(201, 100%, 50%);
        transition: all ease-in-out 300ms;
      }

      textarea {
        min-height: 100px;
        width: 100%;
        outline: 0;
        border-width: 0 0 2px;
        border-color: hsl(201, 100%, 50%);
        transition: all ease-in-out 300ms;
      }

      .button-container {
        display: flex;
        justify-content: space-between;
      }

      button {
        height: 44px;
        color: white;
        outline: none;
        border: none;
        border-radius: 4px;
        flex-shrink: 0;

        &.add-new-item {
          background-color: hsl(201, 87%, 72%);
          width: 100px;
        }

        &.confirm {
          background-color: hsl(201, 100%, 50%);
        }

        &.cancel {
          background-color: hsl(12, 90%, 61%);
        }
      }
    }
  }

  #delete-role-dialog {
    height: 180px;
    gap: 1rem;

    button {
      height: 44px;
      color: white;
      outline: none;
      border: none;
      border-radius: 4px;
      flex-shrink: 0;

      &.danger {
        background-color: hsl(12, 90%, 61%);
      }

      &.cancel {
        background-color: hsl(200, 40%, 90%);
      }
    }
  }

}
</style>
