<template>
  <div class="q-pa-lg">
    <q-layout
      view="lHh Lpr lff"
      container
      style="height: 800px"
      class="shadow-2 rounded-borders"
    >
      <q-header elevated class="primary">
        <q-toolbar class="glossy">
          <q-toolbar-title>Product List</q-toolbar-title>
          <q-btn flat @click="drawer = !drawer" round dense icon="menu" />
        </q-toolbar>
      </q-header>

      <q-footer elevated class="secondary">
        <q-toolbar class="glossy">
          <q-toolbar-title
            >Copyright © 2021 | Project Description: This is just a simple
            product management app.</q-toolbar-title
          >
        </q-toolbar>
      </q-footer>

      <q-drawer v-model="drawer" show-if-above :width="200" :breakpoint="400">
        <q-scroll-area
          style="
            height: calc(100% - 150px);
            margin-top: 150px;
            border-right: 1px solid #ddd;
          "
        >
          <q-list padding>
            <q-item>
              <q-item-section avatar>
                <q-icon name="view_in_ar" />
              </q-item-section>

              <q-item-section> Products </q-item-section>
            </q-item>
          </q-list>
        </q-scroll-area>

        <q-img
          class="absolute-top"
          src="https://venngage-wordpress.s3.amazonaws.com/uploads/2018/09/Yellow-Desk-Simple-Background-Image.jpg"
          style="height: 150px"
        >
          <div class="absolute-bottom bg-transparent">
            <q-avatar size="56px" class="q-mb-sm">
              <img src="/images/avatar-ginelle.png" />
            </q-avatar>
            <div class="text-weight-bold">Ginelle Pearl Grace Tinio</div>
            <div>Batch 32</div>
          </div>
        </q-img>
      </q-drawer>

      <q-page-container>
        <q-page padding>
          <div id="q-app">
            <div class="q-pa-sm q-gutter-sm">
              <q-table
                title="Stocks"
                :data="data"
                :columns="columns"
                row-key="name"
                :selected-rows-label="getSelectedString"
                selection="multiple"
                :selected.sync="selected"
                binary-state-sort
                :pagination.sync="pagination"
              >
                <template v-slot:top>
                  <q-btn
                    dense
                    color="grey-10"
                    padding="10px 20px"
                    label="Add New Product"
                    @click="show_dialog = true"
                    class="new-prod-btn"
                    icon="add_box"
                  ></q-btn>
                  <q-btn
                    class="export-btn"
                    color="primary"
                    icon-right="archive"
                    label="Export to csv"
                    no-caps
                    @click="exportTable"
                  />

                  <div class="q-pa-sm q-gutter-sm">
                    <q-dialog v-model="show_dialog">
                      <q-card>
                        <q-card-section>
                          <div class="text-h6">Product Details</div>
                        </q-card-section>

                        <q-card-section>
                          <div class="row">
                            <q-input
                              v-model="editedItem.name"
                              label="Product Name"
                            ></q-input>
                            <q-input
                              v-model="editedItem.quantities"
                              label="Quantities"
                            ></q-input>
                            <q-input
                              v-model="editedItem.sold"
                              label="Product Sold"
                            ></q-input>
                            <q-input
                              v-model="editedItem.description"
                              label="Product Description"
                            ></q-input>
                            <q-input
                              v-model="editedItem.price"
                              label="Price"
                            ></q-input>
                          </div>
                        </q-card-section>

                        <q-card-actions align="right">
                          <q-btn
                            flat
                            label="OK"
                            color="primary"
                            v-close-popup
                            @click="addRow"
                          ></q-btn>
                        </q-card-actions>
                      </q-card>
                    </q-dialog>
                  </div>
                </template>

                <template v-slot:body="props">
                  <q-tr :props="props">
                    <q-td class="box">
                      <q-checkbox v-model="props.selected" dense />
                    </q-td>
                    <q-td key="desc" :props="props">
                      {{ props.row.name }}
                      <q-popup-edit v-model="props.row.name">
                        <q-input
                          v-model="props.row.name"
                          dense
                          autofocus
                          counter
                        ></q-input>
                      </q-popup-edit>
                    </q-td>
                    <q-td key="quantities" :props="props">
                      {{ props.row.quantities }}
                      <q-popup-edit
                        v-model="props.row.quantities"
                        title="Update quantities"
                        buttons
                      >
                        <q-input
                          type="number"
                          v-model="props.row.quantities"
                          dense
                          autofocus
                        ></q-input>
                      </q-popup-edit>
                    </q-td>
                    <q-td key="sold" :props="props">
                      {{ props.row.sold }}
                      <q-popup-edit
                        v-model="props.row.sold"
                        title="Update Sold Products"
                        buttons
                      >
                        <q-input
                          type="number"
                          v-model="props.row.sold"
                          dense
                          autofocus
                        ></q-input>
                      </q-popup-edit>
                    </q-td>
                    <q-td key="availability" :props="props">
                      {{
                        (props.row.availability =
                          props.row.quantities - props.row.sold)
                      }}
                      <q-popup-edit v-model="props.row.availability">
                        <q-input
                          type="number"
                          v-model="props.row.availability"
                          dense
                          autofocus
                        ></q-input>
                      </q-popup-edit>
                    </q-td>
                    <q-td key="description" :props="props">
                      <div class="text-pre-wrap">
                        {{ props.row.description }}
                      </div>
                      <q-popup-edit v-model="props.row.description">
                        <q-input
                          type="textarea"
                          v-model="props.row.description"
                          dense
                          autofocus
                        ></q-input>
                      </q-popup-edit>
                    </q-td>
                    <q-td key="price" :props="props">
                      $ {{ props.row.price }}
                      <q-popup-edit
                        v-model="props.row.price"
                        title="Update price"
                        buttons
                        persistent
                      >
                        <q-input
                          type="number"
                          v-model="props.row.price"
                          dense
                          autofocus
                          hint="Use buttons to close"
                        ></q-input>
                      </q-popup-edit>
                    </q-td>

                    <q-td key="actions" :props="props" class="edit-delete">
                      <q-btn
                        size="sm"
                        @click="editItem(props.row)"
                        flat
                        round
                        dense
                        icon="drive_file_rename_outline"
                      ></q-btn>
                      <q-btn
                        @click="deleteItem(props.row)"
                        size="sm"
                        flat
                        round
                        dense
                        icon="delete"
                      ></q-btn>
                    </q-td>
                  </q-tr>
                </template>
              </q-table>
            </div>
          </div>
        </q-page>
      </q-page-container>
    </q-layout>
  </div>
</template>

<script>
import { exportFile } from "quasar";

function wrapCsvValue(val, formatFn) {
  let formatted = formatFn !== void 0 ? formatFn(val) : val;

  formatted =
    formatted === void 0 || formatted === null ? "" : String(formatted);

  formatted = formatted.split('"').join('""');
  /**
   * Excel accepts \n and \r in strings, but some other CSV parsers do not
   * Uncomment the next two lines to escape new lines
   */
  // .split('\n').join('\\n')
  // .split('\r').join('\\r')

  return `"${formatted}"`;
}
export default {
  data() {
    return {
      check: false,
      pagination: {
        rowsPerPage: -1, // current rows per page being displayed
      },
      drawer: false,
      newProd: "",
      show_dialog: false,
      editedIndex: -1,
      editedItem: {
        name: "",
        quantities: 0,
        sold: 0,
        description: "Lorem ipsum dolor",
        price: 0,
      },
      defaultItem: {
        name: "",
        quantities: 0,
        sold: 0,
        description: "",
        price: 0,
      },
      selected: [],
      columns: [
        {
          name: "desc",
          required: true,
          label: "Product Name",
          align: "left",
          field: (row) => row.name,
          format: (val) => `${val}`,
          sortable: true,
        },
        {
          name: "quantities",
          align: "left",
          label: "Quantities",
          field: "quantities",
          sortable: true,
        },
        {
          name: "sold",
          align: "left",
          label: "Product Sold",
          field: "sold",
          sortable: true,
        },
        {
          name: "availability",
          align: "left",
          label: "Remaining Products",
          field: "availability",
          sortable: true,
        },
        {
          name: "description",
          align: "left",
          label: "Product Description",
          field: "description",
          sortable: true,
          style: "width: 10px",
        },
        { name: "price", align: "right", label: "Price", field: "price" },

        {
          name: "actions",
          label: "",
          field: "actions",
        },
      ],
      data: [
        {
          name: "Milk Tea",
          quantities: 159,
          sold: 0,
          description: "Lorem ipsum dolor",
          price: 24,
        },
        {
          name: "Pizza",
          quantities: 237,
          sold: 0,
          description: "Sit amet consectetur",
          price: 37,
        },
        {
          name: "Nachos",
          quantities: 262,
          sold: 0,
          description: "Dipiscing elit sed do",
          price: 23,
        },
        {
          name: "Fries",
          quantities: 305,
          sold: 0,
          description: "Eiusmod tempor incididunt",
          price: 67,
        },
        {
          name: "Ramyun",
          quantities: 356,
          sold: 0,
          description: "Ut labore et dolore",
          price: 49,
        },
        {
          name: "Fish Cake",
          quantities: 375,
          sold: 0,
          description: "Ut enim ad minim",
          price: 94,
        },
        {
          name: "Smoothie",
          quantities: 392,
          sold: 0,
          description: "Duis aute irure",
          price: 98,
        },
        {
          name: "Chicken Wings",
          quantities: 408,
          sold: 0,
          description: "Excepteur sint occaecat",
          price: 87,
        },
        {
          name: "Steak",
          quantities: 452,
          sold: 0,
          description: "Cupidatat non proident",
          price: 51,
        },
        {
          name: "Kimchi",
          quantities: 518,
          sold: 0,
          description: "Anim id est laborum",
          price: 65,
        },
      ],
      prods: [
        // {
        //   title: "Activity 1",
        //   done: false,
        // },
        // {
        //   title: "Activity 2",
        //   done: false,
        // },
        // {
        //   title: "Activity 3",
        //   done: false,
        // },
      ],
    };
  },
  methods: {
    exportTable() {
      // naive encoding to csv format
      const content = [this.columns.map((col) => wrapCsvValue(col.label))]
        .concat(
          this.data.map((row) =>
            this.columns
              .map((col) =>
                wrapCsvValue(
                  typeof col.field === "function"
                    ? col.field(row)
                    : row[col.field === void 0 ? col.name : col.field],
                  col.format
                )
              )
              .join(",")
          )
        )
        .join("\r\n");

      const status = exportFile("table-export.csv", content, "text/csv");

      if (status !== true) {
        this.$q.notify({
          message: "Browser denied file download...",
          color: "negative",
          icon: "warning",
        });
      }
    },
    getSelectedString() {
      // return this.selected.length === 0
      //   ? ""
      //   : `${this.selected.length} product${
      //       this.selected.length > 1 ? "s are out of stock or not available" : " is out of stock or not available"
      //     } | ${this.data.length - this.selected.length} product${
      //       this.selected.length > 1 ? "s are in stock" : " is in stock"
      //     }`;

      return this.selected.length === 0
        ? ""
        : `OUT OF STOCK: ${this.selected.length} | AVAILABLE: ${
            this.data.length - this.selected.length
          } `;
    },
    deleteProd(index) {
      this.prods.splice(index, 1);
      this.$q.notify("Deleted");
    },
    addProd() {
      this.prods.push({
        title: this.newProd,
        done: false,
      });
      this.newProd = "";
    },
    editProd() {},
    addRow() {
      if (this.editedIndex > -1) {
        Object.assign(this.data[this.editedIndex], this.editedItem);
      } else {
        this.data.push(this.editedItem);
      }
      this.close();
    },
    deleteItem(item) {
      const index = this.data.indexOf(item);
      confirm("Are you sure you want to delete this product?") &&
        this.data.splice(index, 1);
    },
    editItem(item) {
      this.editedIndex = this.data.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.show_dialog = true;
    },
    close() {
      this.show_dialog = false;
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      }, 300);
    },
  },
};

function newFunction(index) {
  this.prods.text(index, 1);
}
</script>