<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <div>
      <h3>Trying express graphql with Vue </h3>
      <div>
        Data: {{ example }}
        Name: {{ champ.name }}
        AD: {{ champ.attackDamage }}
      </div>
      <button @click="getLanguage">Get Language</button>
      <button @click="getChampionByName">Get Champ By Name</button>
      <hr>
    </div>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import axios from 'axios';

export default {
  name: 'app',
  components: {
    HelloWorld
  },
  data() {
    return {
      example: '',
      champ: {},
      champion: {}
    }
  },
  methods: {
    async getLanguage() {
      try {
        const res = await axios.post(
          'http://localhost:4000/graphql', {
            query: `{language, getChampions { name, attackDamage } }`
        });
        this.example = res.data.data.language;
        this.champ.name = res.data.data.getChampions[0].name;
        this.champ.attackDamage = res.data.data.getChampions[0].attackDamage
      } catch (e) {
          console.log('err', e)
      }
    },
    async getChampionByName () {
      const res = await axios.post(
              'http://localhost:4000/graphql', {
                query: `
          query GetChampionByName($championName: String!) {
            getChampionByName(name: $championName) {
              name
              attackDamage
            }
          }`,
                variables: {
                  championName: 'Ashe'
                }
              });
      this.champion = res.data.data.getChampionByName
    },
    async updateDamage() {
      const res = await axios.post(`http://localhost:4000/graphql`, {
        query: `
          mutation UpdateAttackDamage($championName: String!,  $attackDamage: Float) {
            updateAttack(name: $championName, attackDamage: $attackDamage) {
              name
              attackDamage
            }
          }`,
        variables: {
          championName: this.name,
          attackDamage: this.attack
        }
      })
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
