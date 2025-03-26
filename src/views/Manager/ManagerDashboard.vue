<template>
  <!-- Content -->
  <div class="mt-2">

    <div class="grid grid-cols-1 gap-8 p-4 lg:grid-cols-2 xl:grid-cols-4">
      <!-- Temperature card -->
      <div class="flex"></div>
      <div class="flex items-center justify-between p-4 bg-white rounded-md dark:bg-darker">
        <div>
          <h6 class="text-xl font-medium leading-none tracking-wider text-gray-500 uppercase dark:text-primary-light">
            Temperature
          </h6>
          <span class="text-md font-semibold">{{ dataInputs.temperature }} °C</span>
        </div>
        <div>
          <span class="fa fa-list-alt w-11 h-11 text-gray-300 dark:text-primary-dark"></span>
        </div>
      </div>

      <!-- Vibration card -->
      <div class="flex items-center justify-between p-4 bg-white rounded-md dark:bg-darker">
        <div>
          <h6 class="text-md font-medium leading-none tracking-wider text-gray-500 uppercase dark:text-primary-light">
            Vibration
          </h6>
          <span class="text-xl font-semibold">{{ dataInputs.vibration }} m/s²</span>
        </div>
        <div>
          <span class="fa fa-list-alt w-11 h-11 text-gray-300 dark:text-primary-dark"></span>
        </div>
      </div>

      <div class="flex"></div>

    </div>

    <!-- Crop Prediction Result -->
    <div class="flex flex-wrap mt-3">
      <div class="w-full xl:w-2/2 mb-4">
        <div class="card border rounded-lg shadow-md">
          <div class="card-body pt-3 text-center" style="overflow-x: scroll;">
            <h3 v-if="isLoading" class="text-blue-600 font-bold">Loading...</h3>

            <template v-if="!isLoading">
              <h2>Motorcycle state</h2>
              <hr>
              <h3 v-if="!isInvalid">
                State: <span class="font-bold text-blue-600">{{ prediction.most_probable_state }}</span>
              </h3>
              <p v-if="!isInvalid" class="text-gray-500">Prediction : {{ prediction.predicted_probability }}</p>
              <h3 v-if="isInvalid" class="text-red-600 font-bold">
                No crop is recommended for the given conditions.
              </h3>
              <p v-if="isInvalid" class="text-gray-500" style="font-size:20px;">
                The environment is unsuitable for farming.
              </p>
            </template>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import { flaskApiUrl, laravelApiUrl } from "../../api";

export default {
  name: "ManagerDashboard",
  data() {
    return {
      isLoading: true,
      isInvalid: false,
      dataInputs: {
        temperature: 0,
        vibration: 0,
      },
      prediction: {
        model_accuracy: 0,
        most_probable_state: "no state",
        predicted_probability: 0
      }
    };
  },
  methods: {
    async fetchDataInputs() {
      try {
        const response = await fetch(`${laravelApiUrl}/getting-Data`);
        const data = await response.json();
        this.dataInputs = data;
        this.checkInvalidConditions();
      } catch (error) {
        console.error("Error fetching crop inputs:", error);
      }
    },
    async fetchCropPrediction() {
      try {
        // Fetch prediction data from the Flask API
        const response = await fetch(`${flaskApiUrl}/state`);
        const data = await response.json();
        
        // Update prediction object with the fetched data
        this.prediction = {
          model_accuracy: data.model_accuracy,
          most_probable_state: data.most_probable_state,
          predicted_probability: data.predicted_probability
        };

        // Check for invalid conditions based on fetched data
        this.checkInvalidConditions();
      } catch (error) {
        console.error("Error fetching crop prediction:", error);
      }
    },
    checkInvalidConditions() {
      const { model_accuracy, predicted_probability } = this.prediction;

      // Set the condition to invalid if model_accuracy is 0 or probability is too low
      this.isInvalid = model_accuracy === 0 || predicted_probability <= 0;

      if (this.isInvalid) {
        this.prediction = {
          model_accuracy: 0,
          most_probable_state: "No recommendation",
          predicted_probability: 0
        };
      }
    }
  },
  mounted() {
    // Fetch crop prediction and data inputs immediately after the component is mounted
    this.fetchCropPrediction();
    this.fetchDataInputs();

    setTimeout(() => {
      this.isLoading = false;  // Hide loading after 3 seconds
    }, 3000);

    // Periodically fetch the crop prediction data every 5 seconds
    setInterval(() => {
      this.fetchCropPrediction();
    }, 5000);

    // Periodically fetch the temperature and vibration data every 5 seconds
    setInterval(() => {
      this.fetchDataInputs();
    }, 5000);
  }
};
</script>
