<template>
  <div>
    <div v-if="error">{{ error }}</div>
    <h3>Add a new record</h3>

    <form @submit.prevent="addRecord">
      <div>
        <label for="record_title">Title</label>
        <input 
          type="text"
          id="record_title"
          autofocus
          autocomplete="off"
          placeholder="Type a record name"
          v-model="newRecord.title">
      </div>

      <div>
        <label for="record_year">Year</label>
        <input 
          type="text"
          id="record_year"
          autofocus
          autocomplete="off"
          placeholder="Year"
          v-model="newRecord.year">
      </div>

      <div>
        <label for="artist">Artist</label>
        <select id="artist" v-model="newRecord.artist">
          <option disabled value="">Select an artist</option>
          <option :value="artist.id" v-for="artist in artists" :key="artist.id">{{ artist.name }}</option>
        </select>
        <p>Don't see an artist? <router-link to="/artists">Create one</router-link></p>
      </div>

      <input type="submit" value="Add Record">
    </form>

    <hr/>

    <ul>
      <li v-for="record in records" :key="record.id" :record="record">
        <div>
          <div>
            <p>
              {{ record.title }} &mdash; {{ record.year }}
            </p>
            <p>{{ getArtist(record) }}</p>
          </div>

          <button @click.prevent="editRecord(record)">Edit</button>
          <button @click.prevent="deleteRecord(record)">Delete</button>
        </div>
        
        <div v-if="record == editedRecord">
          <form action="" @submit.prevent="updateRecord">
            <div>
              <div>
                <label>Title</label>
                <input v-model="record.title">
              </div>

              <div>
                <label>Year</label>
                <input v-model="record.year">
              </div>

              <div>
                <select id="artist_update" v-model="record.artist">
                  <option disabled value="">Select an artist</option>
                  <option :value="artist.id" v-for="artist in artists" :key="artist.id">{{ artist.name }}</option>
                </select>
              </div>

              <input type="submit" value="Update">
            </div>
          </form>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: Records,
  data () {
    return {
      artists: [],
      records: [],
      newRecord: [],
      error: '',
      editedRecord: ''
    }
  },
  created () {
    if (!localStorage.signedIn) {
      this.$router.replace('/')
    } else {
      this.$router.secured.get('/api/v1/records')
        .then(response => { this.records = response.data })
        .catch(error => this.setError(error, 'Something went wrong'))
    }
  },
  methods: {
    setError (error, text) {
      this.error = (error.response && error.response.data && error.response.data.error) || text
    },
  }
}
</script>
