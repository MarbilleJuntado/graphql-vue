<template>
  <div id="app">
    <h3>Example 1</h3>
    <div>
      Data: {{ example1 }}
    </div>
    <button @click="getLanguage">Get Language</button>
    <hr>
    <h3>Example 2</h3>
    <div>
      Data:
      <div v-for="champion in champions" :key="champion.name">
        {{ champion }}
      </div>
    </div>
    <button @click="getChampions">Get Champions</button>
    <hr>
    <h3>Example 3</h3>
    <div>
      Data:
        {{ champion }}
    </div>
    <button @click="getChampionByName">Get Champions</button>
    <hr>
    <h3>Example 4</h3>
    <div>
      Name: <input v-model="name">
      Attack Damage: <input v-model.number="attack">
      <div>
        Data:
        {{ updatedChampion }}
      </div>
    </div>
    <button @click="updateAttackdamage">Update Champion</button>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'app',

  data () {
    return {
      example1: '',
      champions: [], 
      champion: {},
      name: 'Sam',
      updatedChampion: {},
      attack: 5.5
    }
  },

  methods: {
    async getLanguage () {
      try {
        const res = await axios.post(
          'http://localhost:4000/graphql', {
            query: '{ language }'
          }
        )
        this.example1 = res.data.data.language
      } catch (e) {
        console.log(`Error: ${e}`)
      }
    },
    async getChampions () {
      const res = await axios.post(
        'http://localhost:4000/graphql', {
          query: `{
            getChampions{
              name
            }
          }`
        }
      )
      this.champions  = res.data.data
    },
    async getChampionByName () {
      const res  = await axios.post('http://localhost:4000/graphql', {
        query: `
          query GetChampionByName($championName: String!) {
            getChampionByName(name: $championName) {
              name
              attackDamage
            }
          }`,
        variables: {
          championName: 'Sam'
        }
      })
      this.champion = res.data.data.getChampionByName
    },
    async updateAttackdamage () {
      const res = await axios.post('http://localhost:4000/graphql', {
        query: `
          mutation UpdateAttackDamage($championName: String!, $attackDamage: Float) {
            updateAttackDamage(name: $championName, attackDamage: $attackDamage) {
              name
              attackDamage
            }
          }`,
        variables: {
          championName: this.name,
          attackDamage: this.attack
        }
      })
      this.updatedChampion = res.data.data.updateAttackDamage;
    }
  }
}
</script>