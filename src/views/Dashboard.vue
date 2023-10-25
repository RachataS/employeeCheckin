<template>
  <!---HTML-->
  <div>
    <div class="mt-15">
      <Chart1 :height="400"></Chart1>
    </div>
    <div class="mt-5">
      <Table13 :newdata="data" />
    </div>
  </div>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue";
import { initializeApp } from "firebase/app";
import Chart1 from "@/components/widgets/charts/Widget1.vue"
import Table13 from "@/components/widgets/tables/Widget13.vue"
import { getDatabase, ref as dbRef, get } from "firebase/database";

export default defineComponent({
  name: "Dashboard",
  components: {
    Chart1,
    Table13
  },
  data() {
    return {
      fetchedData: null,
      data: ''
    };
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
    // Call the fetchData method using "this" to refer to the component
    await this.fetchData();
    this.data = JSON.stringify(this.fetchedData);

  },
  setup() {
    return {

    };
  },
});
</script>
