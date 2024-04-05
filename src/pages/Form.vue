<template>
  <q-page>
    <q-btn style="margin-top: 10px; margin-left: 20px" color="primary" label="Logout" @click="logout" />
    <div style="display: flex; justify-content: center">
      <div style="max-width: 400px; display: flex; flex-direction: column; justify-content: center">
        <h4>Wpisz tekst do wygenerowania na PDF</h4>
        <q-input
          v-model="inputValue"
          label="Wpisz tekst..."
          filled
          auto-grow
          @keyup.native.enter="submit"
        />
        <q-btn style="margin-top: 5px"
               color="primary"
               label="Generuj"
               @click="submit"
               :disable="inputValue.trim().length === 0"
        />
  <!--      <p v-if="pdfUrl" style="margin-top: 8px;">Otwórz PDF: <a :href="pdfUrl" target="_blank">{{ pdfUrl }}</a></p>-->
        <p v-if="error" style="margin-top: 8px; color: #C10015">{{error}}</p>
        <div v-if="fileUrl" style="display: flex; justify-content: center; margin-top: 10px;">
          <q-btn color="primary" label="Otwórz podgląd pliku" @click="openDialog" />
          <q-dialog v-model="dialogVisible">
            <div class="q-pa-md">
              <iframe :src="fileUrl" style="width: 100%; height: 500px;"></iframe>
            </div>
          </q-dialog>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      inputValue: '',
      pdfUrl: '',
      error: '',
      dialogVisible: false,
      fileUrl: ''
    }
  },
  created () {
    const isAuthenticated = localStorage.getItem('authenticated')
    if (!isAuthenticated || isAuthenticated !== 'true') {
      this.$router.push('/')
    }
  },
  methods: {
    submit () {
      if (this.inputValue.trim().length > 0) {
        const postData = {
          value: this.inputValue.trim()
        }

        const headers = {
          Authorization: `Bearer ${process.env.API_TOKEN}`
        }

        axios.post(process.env.API_URL + '/post.php', postData, { headers })
          .then(response => {
            this.pdfUrl = response.data.pdfUrl
            this.fileUrl = this.pdfUrl
            this.inputValue = ''
          })
          .catch(error => {
            console.error('Error:', error)
            this.error = error
          })
      } else {
        console.log('Input value is empty')
      }
    },
    openDialog () {
      this.dialogVisible = true
    },
    logout () {
      localStorage.removeItem('authenticated')
      this.$router.push('/')
    }
  }
}
</script>
