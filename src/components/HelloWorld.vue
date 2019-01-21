<template class="temp">
  <div class="hello">
    <h1>ООО Horns and hoofs</h1>
    <div class="filter">
      <input class="search" v-model="search"/>
      <select v-model="companySelect">
        <option selected value="all">-Selecet company-</option>
        <option v-for="company in companies" :key="company.id" :value="company.id">{{ company.name }}</option>
      </select>
    </div>
    <div class="list-wrapper">
      <ul>
        <div class="person-temp" v-for="person in paginatedData" :key="person.id">
          <div class="avatar"></div>
          <div class="personId">iD:{{ person.id }}</div>
          <div class="createdAt">Created:{{ person.createdAt }}</div>
          <div class="name-wrapper">
            <span class="firstName">{{ person.firstName }}</span>
            <span class="lastName">{{ person.lastName }}</span>
          </div>
          <div class="companyName">{{ companies[person.companyId].name }}</div>
          <button class="btn btn-edit" @click="editClient(person)">Edit</button>
        </div>
      </ul>
    </div>
    <div>
      <button class="btn-prev" @click="prevPage">
        Prev
      </button>
      <span>{{ pageNumber + 1 }}/{{ pageCount }}</span>
      <button class="btn-next" @click="nextPage">
        Next
      </button>
    </div>
    <div class="modal">
      <h2>{{ clientModel.id ? 'Edit client' : 'New client'}}</h2>
      <label for="firstName">Name</label>
      <input type="text" id="firstName" v-model="clientModel.firstName">
      <label for="lastName">Lastname</label>
      <input type="text" id="lastName" v-model="clientModel.lastName">
      <label for="companySelect">Company</label>
      <select name="companySelect" id="companySelect" v-model="clientModel.companyId">
        <option v-for="company in companies" :key="company.id" :value="company.id">{{ company.name }}</option>
      </select>
      <button class="btn btn-save" @click="saveClient">Save</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  data: function () {
    return {
      clients: [],
      companies: {},
      pageNumber: 0,
      personOnPage: 3,
      companySelect: 'all',
      search: '',
      clientModel: {}
    }
  },
  created () {
    axios.all([
      axios.get('/static/companies.json'),
      axios.get('/static/clients.json')
    ])
      .then(axios.spread((companiesRes, clientsRes) => {
        companiesRes.data.forEach(value => {
          this.companies[value.id] = value
        })
        this.clients = clientsRes.data
      }))
  },
  computed: {
    filteredInfo () {
      let clients = this.clients
      if (this.search) {
        clients = clients.filter(person => {
          return person.firstName.toLowerCase().includes(this.search.toLowerCase()) || person.lastName.toLowerCase().includes(this.search.toLowerCase())
        })
      }
      if (this.companySelect && this.companySelect !== 'all') {
        clients = clients.filter(person => {
          return person.companyId === this.companySelect
        })
      }
      return clients
    },
    pageCount () {
      return Math.ceil(this.filteredInfo.length / this.personOnPage)
    },
    paginatedData () {
      let start = this.pageNumber * this.personOnPage
      let end = start + this.personOnPage
      return this.filteredInfo.slice(start, end)
    }
  },
  methods: {
    nextPage () {
      if (this.pageNumber < (this.pageCount - 1)) {
        this.pageNumber++
      }
    },
    prevPage () {
      if ((this.pageNumber - 1) > -1) {
        this.pageNumber--
      }
    },
    saveClient () {
      if (this.clientModel.id) {
        let clientIndex = this.clients.findIndex(value => (value.id === this.clientModel.id))
        let editClient = Object.assign({}, this.clientModel)
        this.clients[clientIndex] = editClient
        this.$set(this.clients, clientIndex, editClient)
      } else {
        let addClient = Object.assign({}, this.clientModel)
        addClient.id = Math.random()
        addClient.createdAt = (new Date()).toISOString()
        this.clients.push(addClient)
      }
      this.clientModel = {}
    },
    editClient (client) {
      this.clientModel = Object.assign({}, client)
    }

  },

  watch: {
    search: function () {
      this.pageNumber = 0
    },
    companySelect: function () {
      this.pageNumber = 0
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  @import url('https://fonts.googleapis.com/css?family=Roboto+Condensed:400,700&subset=cyrillic');
  .filter {
    padding-bottom: 20px;
  }
  .list-wrapper {
    padding: 20px
  }
  .person-temp {
    position: relative;
    background-color: deepskyblue;
    border-bottom: 4px solid deepskyblue;
    border: 1px solid deeppink;
    border-radius: 50px;
    padding-top: 20px;
    padding-bottom: 10px;
    margin: 10px;
    margin-left: 30%;
    margin-right: 30%;
    color: lightcyan;
    box-shadow:
      inset 0 0 50px #fff,
      inset 20px 0 80px #f0f,
      inset -20px 0 80px #0ff,
      inset 20px 0 300px #f0f,
      inset -20px 0 300px #0ff,
      0 0 50px #fff,
      -10px 0 80px #f0f,
      10px 0 80px #0ff;
  }
  .person-temp:hover {
    margin-left: 25%;
    margin-right: 25%;
  }
  .createdAt {
    position: absolute;
    right: 60px;
    top: 20px;
    opacity: 0.5;
  }
  .personId {
    position: absolute;
    left: 80px;
    font-size: 20px;
    opacity: 0.5;
  }
  .name-wrapper {
    padding-top: 30px;
    font-size: 20px;
  }
  .companyName {
    padding-bottom: 8px;
  }
  .btn {
    min-width: 60px;
    min-height: 25px;
    position: relative;
    display: inline-block;
    color: blueviolet;
    font-weight: bold;
    text-decoration: none;
    text-shadow: rgba(255,255,255,.5) 1px 1px, rgba(100,100,100,.3) 3px 7px 3px;
    user-select: none;
    padding: 5px 20px;
    outline: none;
    border-radius: 3px / 100%;
    background-image:
      linear-gradient(45deg, rgba(255,255,255,.0) 30%, rgba(255,255,255,.8), rgba(255,255,255,.0) 70%),
      linear-gradient(to right, rgba(255,255,255,1), rgba(255,255,255,0) 20%, rgba(255,255,255,0) 90%, rgba(255,255,255,.3)),
      linear-gradient(to right, rgba(125,125,125,1), rgba(255,255,255,.9) 45%, rgba(125,125,125,.5)),
      linear-gradient(to right, rgba(125,125,125,1), rgba(255,255,255,.9) 45%, rgba(125,125,125,.5)),
      linear-gradient(to right, rgba(223,190,170,1), rgba(255,255,255,.9) 45%, rgba(223,190,170,.5)),
      linear-gradient(to right, rgba(223,190,170,1), rgba(255,255,255,.9) 45%, rgba(223,190,170,.5));
    background-repeat: no-repeat;
    background-size: 200% 100%, auto, 100% 2px, 100% 2px, 100% 1px, 100% 1px;
    background-position: 200% 0, 0 0, 0 0, 0 100%, 0 4px, 0 calc(100% - 4px);
    box-shadow: rgba(0,0,0,.5) 3px 10px 10px -10px;
  }
  .btn:hover {
    transition: .5s linear;
    background-position: -200% 0, 0 0, 0 0, 0 100%, 0 4px, 0 calc(100% - 4px);
    cursor: pointer;
  }
  .btn:active {
    top: 1px;
  }
  .modal {
    min-width: 600px;
    max-width: 900px;
    padding-bottom: 0px;
    margin: auto;
    margin-top: 20px;
    margin-bottom: 30px;
    border: 1px solid lightgrey;
    border-radius: 50px;
  }
  .btn-save {
    display: block;
    margin: 20px auto;
  }
  .btn-prev,
  .btn-next {
    box-sizing: content-box;
    background-color: transparent;
    border: 1px solid lightgrey;
  }
  .btn-prev:hover,
  .btn-next:hover {
    box-shadow: 3px 5px 5px -2px deepskyblue;
    cursor: pointer;

  }
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
