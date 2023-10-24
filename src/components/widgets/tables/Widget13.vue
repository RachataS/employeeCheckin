<template>
  <div :class="widgetClasses" class="card">
    <div class="card-header border-0 pt-5">
      <h3 class="card-title align-items-start flex-column">
        <span class="card-label fw-bold fs-3 mb-1">พนักงานบริษัท</span>
        <span class="text-muted mt-1 fw-semobold fs-7">Over 500 orders</span>
      </h3>
      <div class="card-toolbar">
        <button type="button" class="btn btn-sm btn-icon btn-color-primary btn-active-light-primary"
          data-kt-menu-trigger="click" data-kt-menu-placement="bottom-end" data-kt-menu-flip="top-end">
          <KTIcon icon-name="category" icon-class="fs-2" />
        </button>
        <Dropdown2></Dropdown2>
      </div>
    </div>
    <div class="card-body py-3">
      <div v-if="parsedData">
        <div class="table-responsive">
          <table class="table table-row-bordered table-row-gray-100 align-middle gs-0 gy-3">
            <thead>
              <tr class="fw-bold text-muted">
                <th>Date</th>
                <th>Card ID</th>
                <th>Name</th>
                <th>Time In</th>
                <th>Time Out</th>
              </tr>
            </thead>
            <tbody>
              <template v-if="parsedData && parsedData.Datetime && parsedData.Member">
                <template v-for="(item, serialnumber) in parsedData.Member" :key="serialnumber">
                  <tr>
                    <td>
                      <a href="#" class="text-dark fw-bold text-hover-primary fs-6">{{ serialnumber }}</a>
                    </td>
                    <td>
                      <a href="#" class="text-dark fw-bold text-hover-primary d-block mb-1 fs-6">{{ item.Name }}</a>
                    </td>
                    <td>
                      <a v-if="parsedData.Datetime['8-10-2023'][serialnumber]?.Checkin"
                        class="text-dark fw-bold text-hover-primary d-block mb-1 fs-6">
                        {{ parsedData.Datetime['8-10-2023'][serialnumber].Checkin.Time }}
                      </a>
                      <span v-else>-</span>
                    </td>
                    <td>
                      <a v-if="parsedData.Datetime['8-10-2023'][serialnumber]?.Checkout"
                        class="text-dark fw-bold text-hover-primary d-block mb-1 fs-6">
                        {{ parsedData.Datetime['8-10-2023'][serialnumber].Checkout.Time }}
                      </a>
                      <span v-else>-</span>
                    </td>
                  </tr>
                </template>
              </template>
            </tbody>
          </table>
        </div>
      </div>
      <div v-else>
        Loading data...
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { defineComponent, ref, type PropType, onMounted } from "vue";
import Dropdown2 from "@/components/dropdown/Dropdown2.vue";
import { getAssetPath } from "@/core/helpers/assets";
import { initializeApp } from "firebase/app";
import { getDatabase, ref as dbRef, get } from "firebase/database";
import { number } from "yup";

export default defineComponent({
  name: "kt-widget-13",
  components: {
    Dropdown2,
  },
  props: {
    widgetClasses: String,
    newdata: String,
  },
  data() {
    return {
      fetchedData: null,
      data: ''
    };
  },
  computed: {
    parsedData() {
      try {
        return JSON.parse(this.data);
      } catch (error) {
        console.error("Error parsing data:", error);
        return null;
      }
    },
  },
  methods: {
    async fetchData() {
      const firebaseConfig = {
        apiKey: "AIzaSyAzugDwrepHZdvszOSFyGRga3IEg1qV-3c",
        authDomain: "rfid-database-8c0f2.firebaseapp.com",
        databaseURL: "https://rfid-database-8c0f2-default-rtdb.asia-southeast1.firebasedatabase.app",
        projectId: "rfid-database-8c0f2",
        storageBucket: "rfid-database-8c0f2.appspot.com",
        messagingSenderId: "509413991821",
        appId: "1:509413991821:web:fef2ffd91ade42f62fc9c4",
        measurementId: "G-LMGX06N1SW"
      };

      // Initialize Firebase
      const app = initializeApp(firebaseConfig);

      // Access the Realtime Database
      const db = getDatabase(app);
      const dataRef = dbRef(db, "RFID"); // Adjust the path as needed
      try {
        const snapshot = await get(dataRef);
        if (snapshot.exists()) {
          // Data found
          this.fetchedData = snapshot.val(); // Store data in the component's data property

        } else {
          console.log("No data found.");
        }
      } catch (error) {
        console.error("Error reading data:", error);
      }
    },
  },
  async mounted() {
    console.log('13 = ' + this.newdata);
    await this.fetchData();
    this.data = JSON.stringify(this.fetchedData);
    console.log("Q13 = " + this.data);
  },
  setup() {
    return {
      getAssetPath,
    };
  },
});
</script>


<style>
.card {
  font-family: 'Noto Sans Thai', sans-serif;
}
</style>
