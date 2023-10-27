<template>
  <!---HTML-->

  <div :class="widgetClasses" class="card">
    <div>
      <h3 class="mt-15">
        <span class="card-label fw-bold fs-3 mb-1">{{ dateToRetrieve }}</span>
      </h3>
      <div class="mt-15">

        <apexchart type="pie" width="380" :options="chartOptions" :series="series"></apexchart>
      </div>

    </div>
  </div>

  <div :class="widgetClasses" class="card">

    <Table13 />

  </div>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue";
import { initializeApp } from "firebase/app";
import Chart1 from "@/components/widgets/charts/Widget1.vue"
import Table13 from "@/components/widgets/tables/Widget13.vue"
import { getDatabase, ref as dbRef, get, connectDatabaseEmulator } from "firebase/database";
import { date } from "yup";

export default defineComponent({
  name: "Dashboard",
  components: {
    Chart1,
    Table13
  },
  data() {
    return {
      fetchedData: null,
      data: [],
      series: [3, 0],
      chartOptions: {
        chart: {
          width: 380,
          type: 'pie',
        },
        labels: ['All employee', 'Employee check in'],
        responsive: [{
          breakpoint: 480,
          options: {
            chart: {
              width: 200
            },
            legend: {
              position: 'bottom'
            }
          }
        }]
      },
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
    dateToRetrieve() {
      const currentDate = new Date();
      const year = currentDate.getFullYear();
      const month = String(currentDate.getMonth() + 1).padStart(2, '0');
      const day = String(currentDate.getDate()).padStart(2, '0');
      return `${day}-${month}-${year}`;
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
    await this.fetchData();
    this.data = JSON.stringify(this.fetchedData);
    console.log(Object.keys(this.parsedData.Datetime));

    if (this.parsedData.Datetime && this.parsedData.Datetime[this.dateToRetrieve]) {
      // Get the objects for the specified date
      const objects = this.parsedData.Datetime[this.dateToRetrieve];

      // Push the count of objects into the series
      this.series[1] = Object.keys(objects).length;
    } else {
      // Handle the case where the date is not found
      console.log(`No data found for date: ${this.dateToRetrieve}`);
    }

    setInterval(async () => {
      await this.fetchData();
      this.data = JSON.stringify(this.fetchedData);
      if (this.parsedData.Datetime && this.parsedData.Datetime[this.dateToRetrieve]) {
        // Get the objects for the specified date
        const objects = this.parsedData.Datetime[this.dateToRetrieve];
        if (this.series[1] < Object.keys(objects).length) {
          this.series[0]--;
          this.series[1] = Object.keys(objects).length;
          console.log(this.series);
        }
        console.log(this.series[1]);
      }
    }, 1000);
  },


  setup() {
    return {

    };
  },
});
</script>
