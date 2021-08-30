<template>
  <div class="home">
    <FilterNav @filterChange="current = $event" :current="current" />
    <div v-if="projects.length">
      <div v-for="project in filterProjects" :key="project.id">
        <SingleProject :project="project" @delete="handleDelete" @complete="handleComplete" />
      </div>
    </div>
  </div>
</template>

<script>
import SingleProject from '../components/SingleProject'
import FilterNav from '../components/FilterNav'

export default {
  name: 'Home',
  data() {
    return {
      projects: [],
      current: 'all'
    }
  },
  components: {
    SingleProject,
    FilterNav
  },
  mounted() {
    fetch('http://localhost:3000/projects').then((res) => {
      return res.json()
    }).then((data) => {
      this.projects = data
    }).catch((err) => {
      console.log(err.message)
    })
  },
  methods: {
    handleDelete(id) {
      this.projects = this.projects.filter((project) => {
        return project.id !== id
      })
    },
    handleComplete(id) {
      let p = this.projects.find(project => {
        return project.id === id
      })
      p.complete = !p.complete
    }
  },
  computed: {
    filterProjects() {
      if(this.current === 'completed') {
        return this.projects.filter((project) => {
          return project.complete
        })
      }
      if(this.current === 'ongoing') {
        return this.projects.filter((project) => {
          return !project.complete
        })
      }
      return this.projects
    }
  }
}
</script>
