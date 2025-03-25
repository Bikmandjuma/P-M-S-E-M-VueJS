<template>
    <div class="home">
      
           <!-- Hero Section -->
          <section>
              <br>
              <div class="about">
                  <div class="container">
                  <br>
                      <div class="row align-items-center">
                          <div class="col-lg-12">
                              <div class="section-header text-center" style="margin-top: -20px;">
                                  <p id="ad_title" class="inline-block mt-1 text-2xl font-bold tracking-wider bg-gradient-to-b from-green-900 to-gray-800 bg-clip-text text-transparent" style="font-family: sa;">About System</p>
                              </div>
                          </div>
                      </div>
                      <div class="row align-items-center">
                          <div class="col-lg-6">
                              <div class="about-img">
                                  <img src="../../assets/Homepage_images/motor1.jpg" alt="Image" class="p-6">
                              </div>
                          </div>
                          <div class="col-lg-6">
                              <div class="flex-1 h-full max-w-4xl p-3 mx-auto bg-white rounded-lg shadow-xl dark:bg-gray-800 pb-2" style="max-height: 600px;overflow:scroll;">
                                  <h4>Predictive Maintenance System for Electric Motorcycles</h4>
                                  <p>Electric motorcycles are a sustainable transportation solution, but maintaining their performance requires proactive diagnostics and timely servicing. This system integrates IoT and AI to predict maintenance needs, enhancing vehicle longevity and safety.</p>
                                  <br>
                                  <h4>Key Features</h4>
                                  <ul style="list-style-type:square;">
                                      <li><strong>Real-time Monitoring:</strong> IoT sensors track battery health, motor performance, and temperature fluctuations.</li>
                                      <li><strong>Predictive Analytics:</strong> AI algorithms analyze collected data to predict component failures before they happen.</li>
                                      <li><strong>Cloud-Based Data Storage:</strong> Enables secure storage, remote access, and efficient data processing.</li>
                                      <li><strong>Mobile & Web Application:</strong> Intuitive dashboards provide real-time status updates and maintenance alerts.</li>
                                      <li><strong>Cost Efficiency:</strong> Reduces maintenance costs and extends the vehicle’s lifespan.</li>
                                  </ul>
                                  <br>
                                  <h4>How It Works</h4>
                                  <ol style="list-style-type:square;">
                                      <li><strong>Data Collection:</strong> IoT sensors capture real-time performance metrics.</li>
                                      <li><strong>Data Transmission:</strong> The collected data is sent to the cloud for processing.</li>
                                      <li><strong>AI Analysis:</strong> Machine learning models analyze patterns to detect anomalies.</li>
                                      <li><strong>Alerts & Recommendations:</strong> Users receive notifications for maintenance needs before issues arise.</li>
                                  </ol>
                                  <br>
                                  <h4>Target Users</h4>
                                  <ul style="list-style-type:square;">
                                      <li>Electric motorcycle owners</li>
                                      <li>Fleet managers and logistics companies</li>
                                      <li>Motorcycle manufacturers and repair centers</li>
                                  </ul>
                                  <br>
                                  <h4>Technologies Used</h4>
                                  <h7>IoT Sensors:</h7>
                                  <ul style="list-style-type:square;">
                                      <li>Battery voltage and temperature sensors</li>
                                      <li>Motor efficiency and vibration sensors</li>
                                      <li>GPS tracking for real-time location</li>
                                  </ul>
                                  <br>
                                  <h4>AI Models Implemented:</h4>
                                  <ul style="list-style-type:square;">
                                      <li>Neural Networks for predictive maintenance</li>
                                      <li>Regression models for performance analysis</li>
                                      <li>Decision Trees for fault detection</li>
                                  </ul>
                                  <br>
                                  <h4>Cloud Technologies:</h4>
                                  <ul style="list-style-type:square;">
                                      <li>AWS IoT Core / Google Cloud IoT</li>
                                      <li>Firebase for real-time data access</li>
                                  </ul>
                                  <br>
                                  <h4>User Interface:</h4>
                                  <ul style="list-style-type:square;">
                                      <li>Web-based dashboard (VueJS, Flask, Laravel)</li>
                                      <li>Mobile application (Android/iOS)</li>
                                  </ul>
                                  <br>
                                  <h4>Expected Impact</h4>
                                  <ul style="list-style-type:square;">
                                      <li>Improved safety and reliability for electric motorcycles.</li>
                                      <li>Reduction in repair costs through proactive maintenance.</li>
                                      <li>Enhanced sustainability by extending battery and motor lifespan.</li>
                                  </ul>
                                  <br>
                                  <p class="text-center items-center justify-center">---- End ----</p>
                              </div>
                          </div>
                      </div>
                  </div>
              </div>
          </section>
    </div>
</template>

<script>
export default {
    name: 'HomeView',
    data(){
      return {
      }
    },
};
</script>

<style scoped>
h4, h6 {
    font-family: sa;
    font-weight: bold;
}

#ad_title {
    position: relative;
    display: inline-block;
}

#ad_title::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 50%;
    height: 2px;
    background-color: black;
}
</style>
