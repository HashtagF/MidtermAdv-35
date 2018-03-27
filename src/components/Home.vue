<template>
  <div class="Home container">
    <div class="row pb-5">
      <div class="col-12">
        <h1 class="text-left">phoneBook</h1>
        <hr>
      </div>
    </div>
    <div class="row">
      <div class="col-1">
        #
      </div>
      <div class="col-3">
        ชื่อ
      </div>
      <div class="col-3">
        เบอร์โทร
      </div>
      <div class="col-3">
        Email
      </div>
      <div class="col-1">
        Edit
      </div>
      <div class="col-1">
        Delete
      </div>
    </div>
    <div class="loop" v-for="(phoneBook, index) in phoneBooks" :key="index">
      <form @submit="updatePhoneBook(phoneBook ,phoneBook.name, phoneBook.tel, phoneBook.email)">
        <div class="row updatePhoneBook border-top pt-4 pb-3"  v-if="phoneBook.statusEdit">
          <div class="col-1">
            {{index + 1}}
          </div>
          <div class="col-3">
            <b-form-input type="text"
                    placeholder="ชื่อ"
                    icon="check"
                    size="is-large"
                    v-model="phoneBook.name">
            </b-form-input>
          </div>
          <div class="col-3">
            <b-form-input type="tel"
                    placeholder="เบอร์โทร"
                    icon="check"
                    size="is-large"
                    v-model="phoneBook.tel">
            </b-form-input>
          </div>
          <div class="col-3">
            <b-form-input type="email"
                    placeholder="Email"
                    icon="check"
                    size="is-large"
                    v-model="phoneBook.email">
            </b-form-input>
          </div>
          <div class="col-1">
            <button type="submit" class="btn btn-info">Update</button>
          </div>
          <div class="col-1">
            <button type="button" class="btn btn-danger" @click="cancelUpdate(phoneBook)">Cancel</button>
          </div>
        </div>
      </form>
      <div class="row showPhoneBook border-top pt-4 pb-3" v-if="phoneBook.statusEdit == false">
        <div class="col-1">
          {{index + 1}}
        </div>
        <div class="col-3">
          {{phoneBook.name}}
        </div>
        <div class="col-3">
          {{phoneBook.tel | formatTel}}
        </div>
        <div class="col-3">
          {{phoneBook.email}}
        </div>
        <div class="col-1">
          <button type="button" class="btn btn-info" @click="editPhoneBook(phoneBook)">Edit</button>
        </div>
        <div class="col-1">
          <button type="button" class="btn btn-danger" @click="removePhoneBook(phoneBook)">Delete</button>
        </div>
      </div>
    </div>
    <form @submit="insertNewPhoneBook()">
      <div class="row newPhoneBook pt-5 ">
        <div class="col-3">
          <b-form-input type="text"
                  placeholder="ชื่อ"
                  icon="check"
                  size="is-large"
                  v-model="newName">
          </b-form-input>
        </div>
        <div class="col-3">
          <b-form-input type="tel"
                  placeholder="เบอร์โทร"
                  icon="check"
                  size="is-large"
                  v-model="newTel">
          </b-form-input>
        </div>
        <div class="col-3">
          <b-form-input type="email"
                  placeholder="Email"
                  icon="check"
                  size="is-large"
                  v-model="newEmail">
          </b-form-input>
        </div>
        <div class="col-1">
          <button type="submit" class="btn btn-secondary">+ Add</button>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import Firebase from 'firebase'
var config = {
  apiKey: 'AIzaSyCmWtP5PY9Jezs94HjrzvfdxNIAhKcDDXU',
  authDomain: 'midtermadv.firebaseapp.com',
  databaseURL: 'https://midtermadv.firebaseio.com',
  projectId: 'midtermadv',
  storageBucket: 'midtermadv.appspot.com',
  messagingSenderId: '498210216713'
}
let app = Firebase.initializeApp(config)
let db = app.database()
let phoneBookRef = db.ref('phoneBooks')

export default {
  name: 'Home',
  firebase: {
    phoneBooks: phoneBookRef
  },
  data () {
    return {
      phoneBookNode: [],
      newName: '',
      newTel: '',
      newEmail: ''
    }
  },
  methods: {
    insertNewPhoneBook () {
      if (this.checkMobileNumber(this.newTel)) {
        phoneBookRef.push({
          name: this.newName,
          tel: this.newTel,
          email: this.newEmail,
          statusEdit: false
        })
        this.newName = ''
        this.newTel = ''
        this.newEmail = ''
      }
    },
    removePhoneBook (phoneBook) {
      phoneBookRef.child(phoneBook['.key']).remove()
    },
    editPhoneBook (phoneBook) {
      phoneBookRef.child(phoneBook['.key'] + '/statusEdit').set(true)
    },
    updatePhoneBook (phoneBook, name, tel, email) {
      if (this.checkMobileNumber(tel)) {
        phoneBookRef.child(phoneBook['.key'] + '/name').set(name)
        phoneBookRef.child(phoneBook['.key'] + '/tel').set(tel)
        phoneBookRef.child(phoneBook['.key'] + '/email').set(email)
        this.cancelUpdate(phoneBook)
      }
    },
    cancelUpdate (phoneBook) {
      phoneBookRef.child(phoneBook['.key'] + '/statusEdit').set(false)
    },
    checkMobileNumber (tel) {
      var flag = false
      if (tel.length !== 10) {
        alert('กรอกเบอร์โทร 10 หลัก')
        return false
      }
      for (var i = 0; i < tel.length; i++) {
        if (tel[i] > 46 || tel[i] < 58) {
          flag = true
        } else {
          flag = false
          alert('กรอกเป็นตัวเลข')
          return false
        }
      }
      if (flag) {
        return flag
      } else {
        alert(alert('กรอกเป็นตัวเลข'))
        return false
      }
    }
  },
  filters: {
    formatTel: function (string) {
      return string.substring(0, 3) + '-' + string.substring(3, 6) + '-' + string.substring(6, 10)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
