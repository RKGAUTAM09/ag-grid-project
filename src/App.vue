<template>
   <div class="main-page">
       <div class="btn-cont">
          <button class="btn" @click="addNewRow()">Add Row</button>
          <button class="btn" @click="deleteSelected()">Delete Selected Rows</button>
          <button class="btn" @click="deleteNonSelected()">Delete Non Selected Rows</button>
          <button class="btn" @click="submit()">Submit</button>
       </div>
       <ag-grid-vue style="width: 90%; height: 50%;"
                    class="ag-theme-alpine"
                    :columnDefs="columnDefs"
                    :rowData="rowData"
                    rowSelection="multiple"
                    :singleClickEdit="true"
                    @grid-ready="onGridReady"
                    :gridOptiopns="topOptions"
                    @cell-clicked="onCellClicked">
       </ag-grid-vue>
       <h1 style="align-self:flex-start; font-size:20px;padding-left:5%;">Submitted Data</h1>
       <ag-grid-vue style="width: 90%; height: 50%;"
                    class="ag-theme-alpine"
                    :columnDefs="columnDefs1"
                    :rowData="rowData1"
                    @grid-ready="onGridReady1"
                    :gridOptions="bottomOptions">
       </ag-grid-vue>
   </div>
</template>

<script>
   import {AgGridVue} from "ag-grid-vue";
   
    function actionCellRenderer(params) {
      let eGui = document.createElement("div");
      eGui = `
          <button class="action-button delete" data-action="delete" > delete </button>
        `;
      return eGui;
    }
    const cityEditorParams = (params) => {
      const selectedCountry = params.data.country;
      const allowedCities = countyToCityMap(selectedCountry);
      return {
        values: allowedCities,
        formatValue: (value) => `${value}`,
      };
    };
    function countyToCityMap(match) {
      const map = {
        Ireland: ["Dublin", "Cork", "Galway"],
        USA: ["New York", "Los Angeles", "Chicago", "Houston"],
        India:["Mumbai", "Delhi", "Kolkata"],
        England:["London", "Liverpool", "Manchester"],
      };
      return match == "" ? [] : map[match];
    }

    const emptyClassRules = {
      "cell-red": (params) => {
        return params.value == null ? 0 : params.value === "" ? 1 : 0;
      },
    }

    const nameClassRules = {
      "cell-red": (params) => {
        return params.value == null ? 0 : params.value === "" ? 1 : 0;
      },
      "cell-yellow": (params) => {
        return params.value == null
          ? 0
          : params.value.length > 0
          ? params.value.length <= 2
            ? 1
            : 0
          : 0;
      },
    };
    const emailClassRules = {
      "cell-yellow": (params) => {
        return params.value.length == 0
          ? 0
          : !params.value.match(
              /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
            );
      },
      "cell-yellow": (params) => {
        return params.value == null
          ? 0
          : params.value.length > 0
          ? params.value.length <= 2
            ? 1
            : 0
          : 0;
      },
      "cell-red": (params) => {
        return params.value == null ? 0 : params.value === "" ? 1 : 0;
      },
    };

   export default {
       name: "App",
       data() {
           return {
               columnDefs: null,
               columnDefs1:null,
               rowData: null,
               rowData1:null,
               gridApi: null,
               columnApi: null,
               gridApi1:null,
               columnApi1:null,
                topOptions: {
                    defaultColDef: {
                        sortable: true,
                        resizable: true,
                        filter: true,
                    },
                },
                bottomOptions: {
                    defaultColDef: {
                        sortable: true,
                        filter: true,
                        maxWidth:150
                    }
                },
           };
       },
       components: {
           AgGridVue,
       },
       mounted(){
         if("[]"!=(localStorage.getItem("r2") || "[]")){
            this.rowData1 = JSON.parse(localStorage.getItem("r2") || "[]");
            this.rowData = JSON.parse(localStorage.getItem("r2") || "[]");
         }
         else{
            this.rowData = [
              { id: "1", name: "Ram", email: "fhvefhe@gmail.com", gender:"Male", dob:"12-12-2000", country:"USA", city:"Los Angeles" },
              { id: "2", name: "qjhbef", email: "qwhbfjhbf@gmail.com", gender:"Female", dob:"31-01-2003", country:"India", city:"Mumbai" },
              { id: "3", name: "qwdggjf", email: "wehfbjhebf@gmail.com", gender:"Male", dob:"22-02-2020", country:"England", city:"Manchester" },
            ];
            this.rowData1 = [];
         }
       },
       beforeMount() {
            
            this.columnDefs = [
                {headerName: "ID", field: "id",resizable:true,editable:true, checkboxSelection: true, cellClassRules:emptyClassRules,},
                {
                  headerName: "Name", 
                  field: "name", 
                  resizable:true,
                  editable:true,
                  cellClassRules:nameClassRules,  
                },
                {
                  headerName: "Email", 
                  field: "email", 
                  resizable:true,
                  editable:true,
                  cellClassRules:emailClassRules,
                },
                {
                  headerName: "Gender",
                  field: "gender",
                  editable:true,
                  cellEditor: "agRichSelectCellEditor",
                  cellClassRules:emptyClassRules,
                  cellEditorParams: {
                    cellHeight: 40,
                    values: ["Male", "Female"],
                  },
                },
                {headerName: "DOB", field: "dob", resizable:true,editable:true, cellClassRules:emptyClassRules,},
                {
                  headerName: "Country",
                  field: "country",
                  editable:true,
                  cellEditor: "agRichSelectCellEditor",
                  cellClassRules:emptyClassRules,
                  cellEditorParams: { cellHeight: 50, values: ["Ireland", "USA", "India", "England"] },
                },
                {
                  headerName: "City",
                  field: "city",
                  editable:true,
                  cellEditor: "agRichSelectCellEditor",
                  cellClassRules:emptyClassRules,
                  cellEditorParams: cityEditorParams,
                },
                {
                  headerName: "Action",
                  cellRenderer: actionCellRenderer,
                  editable: false,
                },
            ];
            this.columnDefs1 = [
                {headerName:'Id', field: 'id'},
                {headerName:'Name', field: 'name'},
                {headerName:'Email', field: 'email'},
                {headerName:'Gender', field: 'gender'},
                {headerName:'DOB', field: 'dob'},
                {headerName:'Country', field: 'country'},
                {headerName:'City', field: 'city'},
            ];
       },
       methods: {
         onCellClicked(params) {
              let action = params.event.target.dataset.action;
              console.log(params);
              if (action === "delete") {
                const del = params.node.data;
                let rem = [];
                this.rowData.forEach((x) => {
                  if (x.id != del.id) {
                    rem.push(x);
                  }
                });
                this.rowData = rem;
                localStorage.setItem("r1", JSON.stringify(this.rowData));
                params.api.applyTransaction({
                  remove: [params.node.data],
                });
              }
            },
           onGridReady(params) {
               this.gridApi = params.api;
               this.columnApi = params.columnApi;
               params.api.sizeColumnsToFit()
           },
           onGridReady1(params) {
               this.gridApi1 = params.api;
               this.columnApi1 = params.columnApi;
           },
           deleteSelected() {
                const selectedNodes = this.gridApi.getSelectedNodes();
                const selectedData = selectedNodes.map((node) => node.data);
                let rem = [];
                this.rowData.forEach((x) => {
                  let g = "0";
                  selectedData.forEach((y) => {
                    if (x.id == y.id) {
                      g = "1";
                    }
                  });
                  if (g == "0") {
                    rem.push(x);
                  }
                });
                this.rowData = rem;
                localStorage.setItem("r1", JSON.stringify(this.rowData));
                this.gridApi.applyTransaction({ remove: selectedData });
           },
           deleteNonSelected() {
            const selectedNodes = this.gridApi.getSelectedNodes();
            const selectedData = selectedNodes.map((node) => node.data);
            this.rowData = selectedData;
            localStorage.setItem("r1", JSON.stringify(this.rowData));
          },
           addNewRow(){
             const emptyrow={
                id: '',
                name: '',
                email: '',
                gender: '',
                dob: '',
                country: '',
                city: '',
              };
              this.rowData.push(emptyrow);
              this.gridApi.applyTransaction({add: [emptyrow]});
           },
            submit() {
              let p = "0";
              this.rowData.forEach((x) => {
              if (
                x.email.match(
                  /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
                ) && x.name.length>2) {}
              else{
                p="1";
              }
              });
              if (p == "1") {
                alert("check error");
                return;
              }
              alert("Submitted");
              localStorage.setItem("r1", JSON.stringify(this.rowData));
              localStorage.setItem("r2", JSON.stringify(this.rowData));
              this.rowData1 = JSON.parse(localStorage.getItem("r2") || "[]");
            },
       },
   };
</script>
<style lang="scss">
  @import "~ag-grid-community/src/styles/ag-grid.scss";
  @import "~ag-grid-community/src/styles/ag-theme-alpine/sass/ag-theme-alpine-mixin.scss";

  .ag-theme-alpine {
      @include ag-theme-alpine((
      ));
  }
  .main-page{
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100vw;
    height: 100vh;
  }
  .btn-cont{
    width: 90%;
    display: flex;
    flex-direction: row;
    margin-top: 10px;
    margin-bottom: 15px;
    justify-content: flex-start;
  }
  .btn{
    padding: 8px;
    border-radius: 2px;
    border:1px solid lightgray;
    background-color: transparent;
    margin-left: 20px;
    margin-right: 20px;
    font-size: 15px;
  }
  button:hover{
    color: blue;
    border: 1px solid blue;
  }
  .cell-red {
      text-align: center;
      background-color: #f71606;
    }
    .cell-yellow {
      text-align: center;
      background-color: #edfd08;
    }
</style>