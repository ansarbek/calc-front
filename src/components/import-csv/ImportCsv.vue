<template>
  <d-card class="card-small mb-3">
    <d-card-header class="border-bottom">
      <h6 class="card-title">CSV Import</h6>
    </d-card-header>
    <d-form class="new-task">
      <d-card-body>
        <div class="form-group">
          <strong class="text-muted d-block mb-2">Выберите файл формата CSV для импорта</strong>
          <div class="custom-file mb-3">
            <input
              type="file"
              id="csv_file"
              name="csv_file"
              class="custom-file-input"
              @change="loadCSV($event)"
            >
            <label for="csv_file" id="csv_file" class="custom-file-label">Выберите файл...</label>
          </div>
        </div>
        <blockquote>
          <p>
            Вы можете скачать
            <d-link
              href="https://app.box.com/s/uwx7i3zi2dww49304rwstcftr03pw68k"
              target="_blank"
              download
            >Этот файл для примера</d-link>&nbsp;и загрузить для примера.
          </p>
        </blockquote>
        <div class="col-sm-offset-3 col-sm-9 mb-2">
          <div>
            <label for="header_rows"></label>
            <d-checkbox inline id="header_rows">File contains header row?</d-checkbox>
          </div>
        </div>
        <!-- <div class="col-sm-offset-3 col-sm-9">
          <a href="#" class="btn btn-primary">Parse CSV</a>
        </div>-->
        <table class="meta-table" v-if="parse_csv">
          <thead>
            <tr>
              <th
                v-for="key in parse_header"
                @click="sortBy(key)"
                :class="{ active: sortKey == key }"
                v-bind:key="key"
              >
                {{ key | capitalize }}
                <span
                  class="arrow"
                  :class="sortOrders[key] > 0 ? 'asc' : 'dsc'"
                ></span>
              </th>
            </tr>
          </thead>
          <tr v-for="csv in parse_csv" v-bind:key="csv">
            <td v-for="key in parse_header" v-bind:key="key">{{csv[key]}}</td>
          </tr>
        </table>
        <!-- <pre>{{parse_csv}}</pre> -->
      </d-card-body>
      <d-card-footer>
        <div class="d-flex">
          <d-link @click="goBack">Отменить</d-link>
          <d-button class="btn btn-success ml-auto" type="submit">Сохранить</d-button>
        </div>
      </d-card-footer>
    </d-form>
  </d-card>
</template>

<script>
/* eslint-disable func-names */
/* eslint-disable no-alert */
export default {
  name: 'import-csv',
  data() {
    return {
      channel_name: '',
      channel_fields: [],
      channel_entries: [],
      parse_header: [],
      parse_csv: [],
      sortOrders: {},
      sortKey: '',
    };
  },
  filters: {
    capitalize(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
  },
  methods: {
    // csvToJson.formatValueByType().getJsonFromCsv(fileInputName); If you want that a number will be printed as a Number type and not as a String type, use:
    csvJSON(csv) {
      const vm = this;
      const lines = csv.split('\n');
      const result = [];
      const headers = lines[0].split(',');
      vm.parse_header = lines[0].split(',');
      lines[0].split(',').forEach((key) => {
        vm.sortOrders[key] = 1;
      });

      // eslint-disable-next-line array-callback-return
      lines.map((line, indexLine) => {
        // eslint-disable-next-line array-callback-return
        if (indexLine < 1) return; // Jump header line

        const obj = {};
        const currentline = line.split(',');

        // eslint-disable-next-line array-callback-return
        headers.map((header, indexHeader) => {
          obj[header] = currentline[indexHeader];
        });

        result.push(obj);
      });

      result.pop(); // remove the last item because undefined values

      return result; // JavaScript object
    },
    loadCSV(e) {
      const vm = this;
      if (window.FileReader) {
        const reader = new FileReader();
        reader.readAsText(e.target.files[0]);
        // Handle errors load
        reader.onload = function (event) {
          const csv = event.target.result;
          vm.parse_csv = vm.csvJSON(csv);
        };
        reader.onerror = function (evt) {
          // eslint-disable-next-line eqeqeq
          if (evt.target.error.name == 'NotReadableError') {
            alert('Не могу прочитать файл !');
          }
        };
      } else {
        alert('FileReader не поддерживается в данном браузере!.');
      }
    },
    goBack() {
      this.$router.go(-1);
    },
  },
};

// import CSV as nested JSON for emission sources

// define properly csv
// const records = ['Work Site 1,category2,Facility Name 1, Facility Location 1,Emission Source 1,0001,organized,true,Release Source name 1,01,Electricity',
//   'Work Site 2,category2,Facility Name 2,Facility Location 2,Emission Source 2,6002,unorganized,false,Release Source name 2,01,Some product'];

// const output = records.map((record) => {
//   const arr = record.split(',');
//   return {
//     workSiteName: arr[0],
//     natureUserCategory: arr[1],
//     facility: {
//       facilityName: arr[2],
//       facilityLocation: {
//         facilityLocationName: arr[3],
//         emissionSource: {
//           emissionSourceName: arr[4],
//           emissionSourceNumber: arr[5],
//           emissionSourceType: arr[6],
//           hasTreatmentUnit: arr[7],
//           releaseSource: {
//             releaseSourceName: arr[8],
//             releaseSourceNumber: arr[9],
//             product: arr[10],
//           },
//         },
//       },
//     },
//   };
// });

// console.log(output);

</script>

<style scoped>
.panel {
  border: 2px solid #dfdfdf;
  box-shadow: rgba(0, 0, 0, 0.15) 0 1px 0 0;
  margin: 10px;
}

.panel.panel-sm {
  max-width: 700px;
  margin: 10px auto;
}

.panel-heading {
  border-bottom: 2px solid #dfdfdf;
}

.panel-heading h1,
.panel-heading h2,
.panel-heading h3,
.panel-heading h4,
.panel-heading h5,
.panel-heading h6 {
  margin: 0;
  padding: 0;
}

.panel-body .checkbox-inline {
  padding: 15px 20px;
}

blockquote {
  border: 1px solid #e6e9ec;
  padding-top: 30px;
  padding-bottom: 12px;
  padding-right: 20px;
  padding-left: 20px;
  border-radius: 5px;
  box-shadow: 0 3px 8px 0 rgba(0, 0, 0, 0.03);
  background-color: #fff;
  border-left: 3px solid #2d53fe;
  font-size: 13px;
  line-height: 1;
}

table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td,
th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #dddddd;
}
</style>

