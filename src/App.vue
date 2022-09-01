<template>
  <div>
    <Button
      label="New"
      icon="pi pi-plus"
      class="p-button-success mr-2"
      @click="openModal"
    />

    <Button
      label="Delete"
      icon="pi pi-trash"
      class="p-button-danger"
      @click="confirmDeleteSelected"
      :disabled="!selectedProducts || !selectedProducts.length"
    />

    <span class="ml-2">
      <Button
        icon="pi pi-external-link"
        label="Export"
        @click="exportCSV($event)"
      />
    </span>

    <DataTable
      :value="products"
      responsiveLayout="scroll"
      :paginator="true"
      :rows="4"
      editMode="cell"
      @cell-edit-complete="onCellEditComplete"
      class="editable-cells-table"
      v-model:selection="selectedProducts3"
    >
      <Column
        v-for="col of columns"
        :field="col.field"
        :header="col.header"
        :key="col.field"
        :sortable="true"
        :selectionMode="col.field === 'Title' ? 'multiple' : null"
      >
        <template #body="slotProps" v-if="col.field === 'Rating'">
          <Rating
            :modelValue="slotProps.data.Rating"
            :readonly="true"
            :cancel="false"
          />
        </template>
        <template #editor="{ data, field }">
          <InputText v-model="data[field]" autofocus />
        </template>
      </Column>
      <Column :exportable="false" style="min-width: 8rem">
        <template #body="slotProps">
          <Button
            icon="pi pi-pencil"
            class="p-button-rounded p-button-success mr-2"
            @click="editProduct(slotProps.data)"
          />
          <Button
            icon="pi pi-trash"
            class="p-button-rounded p-button-warning"
            @click="confirmDeleteProduct(slotProps.data)"
          />
        </template>
      </Column>
    </DataTable>

    <Dialog
      header="New Movie"
      v-model:visible="displayModal"
      :breakpoints="{ '960px': '75vw', '640px': '90vw' }"
      :style="{ width: '25vw' }"
      :modal="true"
    >
      <div class="grid p-fluid">
        <div class="col-12 md:col-4">
          <div class="p-inputgroup">
            <span class="p-inputgroup-addon">
              <i class="pi pi-pencil"></i>
            </span>
            <InputText
              placeholder="Title"
              v-model="titleOne"
              ref="inputTitle"
              required
            />
          </div>
        </div>
      </div>
      <div class="grid p-fluid">
        <div class="col-12 md:col-4">
          <div class="p-inputgroup">
            <span class="p-inputgroup-addon">
              <i class="pi pi-pencil"></i>
            </span>
            <InputText
              placeholder="Director"
              v-model="directorOne"
              ref="inputTitle"
            />
          </div>
        </div>
      </div>

      <div class="grid p-fluid">
        <div class="col-12 md:col-4">
          <div class="p-inputgroup">
            <span class="p-inputgroup-addon">
              <i class="pi pi-pencil"></i>
            </span>
            <InputNumber
              inputId="minmax-buttons"
              v-model="yearOne"
              type="number"
              showButtons
              :min="1980"
              :max="2023"
            />
          </div>
        </div>
      </div>

      <div class="grid p-fluid">
        <div class="col-12 md:col-4">
          <div class="p-inputgroup">
            <span class="p-inputgroup-addon">
              <i class="pi pi-pencil"></i>
            </span>
            <Rating v-model="val2" :cancel="false" name="cancel" />
          </div>
        </div>
      </div>

      <div class="grid p-fluid">
        <div class="col-12 md:col-4">
          <div class="p-inputgroup">
            <span class="p-inputgroup-addon">
              <i class="pi pi-pencil"></i>
            </span>
            <Dropdown
              v-model="selectedGenre"
              :options="genres"
              optionLabel="name"
              placeholder="Select Genre"
            />
          </div>
        </div>
      </div>

      <template #footer>
        <Button
          label="No"
          icon="pi pi-times"
          @click="closeModalTwo"
          class="p-button-text"
        />
        <Button label="Yes" icon="pi pi-check" @click="closeModal" autofocus />
      </template>
    </Dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      columns: null,
      selectedProducts3: null,
      val2: 3,
      titleOne: "",
      directorOne: "",
      yearOne: 1980,
      products: [
        {
          Title: "Avengers",
          Director: "Joss Whedon",
          Year: 2012,
          Rating: "4",
          Genre: "Sci-fi",
        },
        {
          Title: "Tenet",
          Director: "Christopher Nolan",
          Year: 2013,
          Rating: 4.5,
          Genre: "Sci-fi",
        },
        {
          Title: "Inception",
          Director: "Christopher Nolan",
          Year: 2014,
          Rating: 3.5,
          Genre: "Sci-fi",
        },
        {
          Title: "seven",
          Director: "David Fincher",
          Year: 2015,
          Rating: 3,
          Genre: "Crime",
        },
        {
          Title: "asd",
          Director: "Joss Whedon",
          Year: 2015,
          Rating: 5,
          Genre: "Sci-fi",
        },
        {
          Title: "deve",
          Director: "Joss Whedon",
          Year: 2002,
          Rating: 2.5,
          Genre: "Sci-fi",
        },
      ],
      displayModal: false,
      selectedGenre: null,
      genres: [
        { name: "Drama", code: "DM" },
        { name: "Sci-fi", code: "SF" },
        { name: "Horror ", code: "HR" },
        { name: "Fantasy ", code: "FT" },
        { name: "Comedy ", code: "CD" },
      ],
    };
  },

  created() {
    this.columns = [
      { field: "Title", header: "Title" },
      { field: "Director", header: "Director" },
      { field: "Year", header: "Year" },
      { field: "Rating", header: "Rating" },
      { field: "Genre", header: "Genre " },
    ];
  },

  methods: {
    openModal() {
      this.displayModal = true;
    },
    closeModal() {
      this.products.push({
        Title: this.titleOne,
        Director: this.directorOne,
        Year: this.yearOne,
        Genre: this.selectedGenre.name,
        Rating: this.val2,
      });
      this.displayModal = false;
      this.titleOne = "";
      this.directorOne = "";
    },
    closeModalTwo() {
      this.displayModal = false;
    },
    onCellEditComplete(event) {
      let { data, newValue, field } = event;

      switch (field) {
        case "Rating":
          if (this.isPositiveInteger(newValue)) data[field] = newValue;
          else event.preventDefault();
          break;
        case "Year":
          if (this.isPositiveInteger(newValue)) data[field] = newValue;
          else event.preventDefault();
          break;

        default:
          if (newValue.trim().length > 0) data[field] = newValue;
          else event.preventDefault();
          break;
      }
    },
    isPositiveInteger(val) {
      let str = String(val);
      str = str.trim();
      if (!str) {
        return false;
      }
      str = str.replace(/^0+/, "") || "0";
      var n = Math.floor(Number(str));
      return n !== Infinity && String(n) === str && n >= 0;
    },
    onRowEditSave(event) {
      let { newData, index } = event;

      this.products2[index] = newData;
    },
  },
};
</script>
