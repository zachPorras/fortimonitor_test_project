<template>
  <div id="papa-div">

    <h1 id="header">FortiMonitor Outages</h1>
    
    <v-btn 
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
    </v-btn>

    <v-progress-circular
      v-if="request_loading==true"
      :value="80"
      color="orange"
      indeterminate
      id="spinner"
    >refreshing</v-progress-circular>

    <v-card 
    v-if="table_display==true" 
    id="response-data-card"
    >

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
      id="table-pagination">
      </v-pagination>
    
    </v-card>

    <v-card 
    v-else-if="error_message"
    id="error-card"
    >
      Problem with API request: {{ error_message }}
    </v-card>

  </div>
</template>


<script>

  export default {
    name: 'RequestBox',
    
    props: {},

    data: function() {
      return {

        // method vars
        url: 'https://api2.panopta.com/v2/outage?api_key=6369950d-c200-47e5-b943-06047662e4fa',
        request_loading: false,

        // response data
        response_data: null,
        data_modified: [],
        data_index: 1,
        
        // table
        table_display: false,
        error_message: null,
        
      };
    },

    methods: {

      // Sends request to panopta endpoint, retrieves outage data
      makeRequest() {
        // if response data is already present, reset all vars
        if(this.data_modified!=null) {
          this.resetVars()
        }

        this.request_loading = true

        let request_params = {
          method: 'GET'
        }
        fetch(this.url, request_params)
          .then(response => response.json())
          .then(data => {

            console.log('initial response object: ', data)
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
              this.request_loading = false
              this.data_modified.push(body)
            })

            console.log('response object modified for table: ', this.data_modified)
            this.table_display = true
          })
          .catch(err => {
            this.request_loading = false
            this.error_message = err
            console.log(err)
          });
      },

      resetVars() {
        this.response_data = null 
        this.data_modified = [] 
        this.table_display = false 
        this.error_message = null
      }

    },

    computed: {

      // Paginates response data
      displayData() {
        this.data_modified[this.data_index-1]
        return this.data_modified[this.data_index-1]
      }

    },

    mounted: {

    },

    beforeDestroy: {

    },

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
  top: 27rem;
}

#response-data-card {
  width: 75rem;
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
