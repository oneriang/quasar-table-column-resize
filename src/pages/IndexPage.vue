<template>
  <q-table ref="tableRef" :rows="rows" :columns="columns" row-key="id" :virtual-scroll="false" :rows-per-page-options="[]"
    :pagination="false" style="width: 100%" separator="cell"/>
</template>



<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

const tableRef = ref(null);

const sortableEnabled = ref(true); // Control sortable dynamically

let a = null;

const rows = ref([
  { id: 1, name: 'John Doe', age: 30, email: 'john.doe@example.com',email1: 'john.doe@example.com' },
  { id: 2, name: 'Jane Smith', age: 25, email: 'jane.smith@example.com',email1: 'john.doe@example.com' },
  { id: 3, name: 'Bob Johnson', age: 40, email: 'bob.johnson@example.com',email1: 'john.doe@example.com' },
]);

const columns = ref([
  { name: 'name', required: true, label: 'Name', align: 'left', field: 'name', sortable: sortableEnabled.value }, // Use dynamic sortable
  { name: 'age', required: true, label: 'Age', align: 'left', field: 'age', sortable: sortableEnabled.value }, // Use dynamic sortable
  { name: 'email', required: true, label: 'Email', align: 'left', field: 'email', sortable: sortableEnabled.value }, // Use dynamic sortable
  { name: 'email1', required: true, label: 'Email', align: 'left', field: 'email', sortable: sortableEnabled.value }, // Use dynamic sortable
]);

onMounted(() => {
  addColumnResizeListeners();
});

onUnmounted(() => {
  removeColumnResizeListeners();
});

function addColumnResizeListeners() {
  const tableHeader = tableRef.value.$el.querySelector('.q-table__middle table thead');

  console.log(tableHeader);


  // const headers = tableHeader.querySelectorAll('.q-table__header th');

  const headers = tableRef.value.$el.querySelectorAll('.q-table__middle table thead tr th');

  console.log(headers);

  headers.forEach((header) => {

    console.log(header);

    header.style.position = 'relative';

    const columnResizer = document.createElement('div');
    columnResizer.className = 'column-resizer';
    header.appendChild(columnResizer);

    let startX = 0;
    let startWidth = 0;
    let columnIdx = -1;

    header.addEventListener('mousedown', (event) => {
      console.log('header mousedown');
      console.log(event);
      if(a) {
        a.sortable = true;
        a = null;
      }
    });

    // header.addEventListener('mouseover', (event) => {
    //   console.log('header mouseover');
    //   console.log(event);
    //   if(a) {
    //     a.sortable = true;
    //     a = null;
    //   }
    // });


    columnResizer.addEventListener('mousedown', (event) => {
      console.log('mousedown');
      event.stopPropagation();
      startX = event.pageX;
      startWidth = header.clientWidth;
      columnIdx = Array.from(header.parentNode.children).indexOf(header);
      console.log(columnIdx);
      sortableEnabled.value = false; // Disable sortable when resizing starts
      tableRef.value.columns[columnIdx].sortable = false;

      a = tableRef.value.columns[columnIdx];

      return false;
    });

    columnResizer.addEventListener('mouseup', (event) => {
      console.log('mouseup');
      columnIdx = Array.from(header.parentNode.children).indexOf(header);
      console.log(columnIdx);
      // tableRef.value.columns[columnIdx].sortable = true;
      return false;
    });

    columnResizer.addEventListener('mouseover', (event) => {
      console.log('mouseover');
      // columnResizer.className = 'column-resizer';
      columnResizer.style.borderRight = "2px solid #c0c0c0";
    });

    columnResizer.addEventListener('mouseout', (event) => {
      console.log('mouseout');
      columnResizer.style.borderRight = "";
    });


    document.addEventListener('mousemove', (event) => {
      console.log('mousemove');
      if (columnIdx > -1) {
        const diff = event.pageX - startX;
        const newWidth = startWidth + diff;

        header.style.width = `${newWidth}px`;
        const cells = tableHeader.querySelectorAll(`.q-table__body td:nth-child(${columnIdx + 1})`);
        cells.forEach((cell) => {
          cell.style.width = `${newWidth}px`;
        });
      }
    });

    document.addEventListener('mouseup', () => {
      console.log('mouseup');
      sortableEnabled.value = true; // Enable sortable when resizing ends
      // tableRef.value.columns[columnIdx].sortable = true;
      // if(a) {
      //   a.sortable = true;
      //   a = null;
      // }
      columnIdx = -1;
    });
  });
}

function removeColumnResizeListeners() {
  // const headers = tableRef.value.$el.querySelectorAll('.q-table__header th');

  const headers = tableRef.value.$el.querySelectorAll('.q-table__middle table thead tr th');
  headers.forEach((header) => {
    header.style.removeProperty('position');
    const columnResizer = header.querySelector('.column-resizer');
    if (columnResizer) {
      columnResizer.remove();
    }
  });

  document.removeEventListener('mousemove', () => { });
  document.removeEventListener('mouseup', () => { });
}
</script>


