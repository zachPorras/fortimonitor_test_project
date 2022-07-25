<template>
  <div id="papa-div">

    <h1 id="header"></h1>
    
    <!-- OLD REQUEST/RESET BTNS -->
    <!-- <v-btn 
    class="request-btns" 
    @click="makeRequest()"
    >
    See Outages
    </v-btn>

    <v-btn 
    class="request-btns"
    id="btn-reset" 
    @click="resetVars()"
    >
    Reset
    </v-btn> -->

    <v-progress-circular
      v-if="request_loading==true"
      :value="80"
      indeterminate
      id="spinner"
    >refreshing</v-progress-circular>

    <v-card 
    id="response-data-card"
    >
      <v-card-title id="card-title">
        FortiMonitor Outages
      </v-card-title>

      <v-card-subtitle>Displaying 10 most recent outages as of {{ latest_request_time }}</v-card-subtitle>

      <v-simple-table>
        <thead id="table-header">
          <tr>
            <th v-for="(value, key) in displayData" :key="key">{{ key }}</th>
          </tr>
        </thead>
        <tbody>
          <tr id="tbody-tr">
            <td 
            v-for="(value, key, index) in displayData" 
            :key="index"
            >
            {{ value }}
            </td>
          </tr>
        </tbody>
      </v-simple-table>

      <v-pagination 
      v-model="data_index" 
      :length="data_modified.length" 
      total-visible="5"
      id="table-pagination"
      dark
      >
      </v-pagination>

      <v-card-actions id="card-footer">data refreshes every 30 seconds</v-card-actions>
    
    </v-card>

    <v-card 
    v-if="error_message"
    id="error-card"
    >
      Problem with recent API request: {{ error_message }}
    </v-card>

  </div>
</template>


<script>

  export default {
    name: 'RequestBox',
    
    props: {},

    data: function() {
      return {

        // hook vars
        timer: null,

        // method vars
        url: 'https://api2.panopta.com/v2/outage?api_key=6369950d-c200-47e5-b943-06047662e4fa',
        request_loading: false,

        // response data
        response_data: null,
        data_modified: [],
        data_index: 1,
        
        // table
        error_message: null,
        latest_request_time: '(awaiting successful response...)',
        
      };
    },

    methods: {

      // Sends request to panopta endpoint, retrieves outage data
      makeRequest() {
        // if response data is already present, reset all vars
        // if(this.data_modified!=null) {
        //   this.resetVars()
        // }
        this.request_loading = true
        let request_params = {
          method: 'GET'
        }
        fetch(this.url, request_params)
          .then(response => response.json())
          .then(data => {
            console.log('initial response object: ', data)
            this.data_modified = []
            data.outage_list.forEach(outage => {
              let body = {
                'server_id': outage['server_id'],
                'server_name': outage['server_name'],
                'description': outage['description'],
                'start_time': outage['start_time'],
                'end_time': outage['end_time'],
                'status': outage['status'],
                'severity': outage['severity'],
                'has_active_maintenance': outage['has_active_maintenance']
              }
              this.data_modified.push(body)
              let current_time = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', second: '2-digit' })
              let current_date = new Date().toLocaleDateString()
              this.latest_request_time = `${current_date} ${current_time}`
              this.request_loading = false
            })
            console.log('response object modified for table: ', this.data_modified)
            
          })
          .catch(err => {
            this.request_loading = false
            this.error_message = err
            console.log('Problem with most recent API call: ', err)
            setTimeout(() => {this.error_message = null}, 10000);
          });
      },

      // resets vars
      // resetVars() {
      //   this.response_data = null 
      //   this.data_modified = [] 
      //   this.error_message = null
      // }

    },

    computed: {
      // Paginates response data
      displayData() {
        this.data_modified[this.data_index-1]
        return this.data_modified[this.data_index-1]
      }

    },

    mounted() {
      try {
        // make initial API request
        this.makeRequest()
        // call API method every 30sec to refresh data
        this.timer = setInterval(() => {
          this.makeRequest()
        }, 30000)
      } catch (err) {
        this.request_loading = false
        this.error_message = err.status
        console.log('Problem with initial API call: ', err)
        // setTimeout(this.error_message = null, 3000)
      }
    },

    beforeDestroy() {
      // removes timer triggering API call when view is destroyed
      clearInterval(this.timer)
    }

  }

</script>


<style scoped>

#papa-div {
  display: relative;
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}

#header {
  margin-bottom: 2rem;
}

#spinner {
  padding-top: 4rem;
  position: absolute;
  justify-self: center;
  top: 24rem;
}

#response-data-card {
  width: 75rem;
}

#card-title {
  justify-content: center;
  font-size: 1.7rem;
}

#card-footer {
  justify-content: center;
  color: rgba(128, 128, 128, 0.788);
  font-size: 0.9rem;
}

#error-card {
  background-color: lightpink;
}

.request-btns {
  width: 12rem;
  margin-bottom: 1rem;
}

#btn-reset {
  margin-bottom: 2rem;
}

#tbody-tr {
  height:6rem;
}

/* #table-header {
  margin-bottom: 1rem;
} */

#table-pagination {
  margin-top: 1rem;
}

</style>
