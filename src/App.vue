<template>
  <div style="display: block; gap: 5px;">
    <div style="display: flex; justify-content: space-between; align-items: center;">
      <h2 style="flex: 1; text-align: center; margin-bottom: 20px; margin-top: 20px;">
        CRUD Application
      </h2>
      <a-button style="margin-bottom: 20px; margin-top: 20px; margin-right: 20px;" type="primary" @click="openAddModal">
        Add Data
      </a-button>
    </div>
    <div style="flex: 1;">
      <a-table :columns="columns" :dataSource="data" rowKey="id">
        <template #bodyCell="{ column, record }">
          <span v-if="column.key === 'actions'">
            <a @click="openEditModal(record)">Edit</a> | 
            <a @click="deleteRecord(record.id)">Delete</a>
          </span>
        </template>
      </a-table>

      <a-modal
        v-model:visible="showModal"
        :title="isEditMode ? 'Edit Record' : 'Add Record'"
        @ok="handleSubmit"
        @cancel="handleCancel"
      >
        <a-form layout="vertical">
          <a-form-item label="Name">
            <a-input v-model:value="formData.name" />
          </a-form-item>
          <a-form-item label="Description">
            <a-input v-model:value="formData.description" />
          </a-form-item>
        </a-form>
      </a-modal>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import { message } from 'ant-design-vue';

export default {
  setup() {
    const showModal = ref(false);
    const isEditMode = ref(false);
    const formData = ref({ id: null, name: '', description: '' });
    const data = ref([
      { id: 1, name: 'Item 1', description: 'Description 1' },
      { id: 2, name: 'Item 2', description: 'Description 2' },
    ]);

    // Define table columns with actions for edit and delete
    const columns = [
      { title: 'Name', dataIndex: 'name', key: 'name' },
      { title: 'Description', dataIndex: 'description', key: 'description' },
      {
        title: 'Actions',
        key: 'actions',
        scopedSlots: { customRender: 'actions' },
      },
    ];

    // Open Add Modal
    const openAddModal = () => {
      isEditMode.value = false;
      formData.value = { id: null, name: '', description: '' };
      showModal.value = true;
    };

    // Open Edit Modal with pre-filled data
    const openEditModal = (record) => {
      isEditMode.value = true;
      formData.value = { ...record };
      showModal.value = true;
    };

    // Handle modal form submission for both add and edit
    const handleSubmit = () => {
      if (formData.value.name && formData.value.description) {
        if (isEditMode.value) {
          const index = data.value.findIndex((item) => item.id === formData.value.id);
          if (index !== -1) {
            data.value[index] = { ...formData.value };
            message.success('Record updated successfully');
          }
        } else {
          data.value.push({
            id: Date.now(),
            name: formData.value.name,
            description: formData.value.description,
          });
          message.success('Record added successfully');
        }

        formData.value = { id: null, name: '', description: '' };
        showModal.value = false;
      } else {
        message.error('Please fill in all fields');
      }
    };

    // Handle Cancel action to close the modal
    const handleCancel = () => {
      showModal.value = false;
    };

    // Delete record by ID
    const deleteRecord = (id) => {
      data.value = data.value.filter((item) => item.id !== id);
      message.success('Record deleted successfully');
    };

    return {
      showModal,
      isEditMode,
      formData,
      data,
      columns,
      openAddModal,
      openEditModal,
      handleSubmit,
      handleCancel,
      deleteRecord,
    };
  },
};
</script>

<style scoped>
</style>
