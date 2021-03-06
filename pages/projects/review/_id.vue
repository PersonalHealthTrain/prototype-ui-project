<template>
  <client-view :title="project.title">

    <!-- Project infomration -->
    <v-container
      grid-list-md
      text-xs-center>
      <v-layout
        row
        wrap>

        <v-flex xs2>
          <v-card>
            <v-card-text><strong>Title</strong></v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs10>
          <v-card>
            <v-card-text>{{ project.title }}</v-card-text>
          </v-card>
        </v-flex>

        <v-flex xs2>
          <v-card>
            <v-card-text><strong>Description</strong></v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs10>
          <v-card>
            <v-card-text>{{ project.description }}</v-card-text>
          </v-card>
        </v-flex>

        <v-flex xs2>
          <v-card>
            <v-card-text><strong>Trains</strong></v-card-text>
          </v-card>
        </v-flex>
        <v-flex xs10>
          <v-card>
            <v-card-text>{{ project.trains }}</v-card-text>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>

    <v-item-group>
      <v-btn @click="submitApproval(true)">Approve</v-btn>
      <v-btn @click="submitApproval(false)">Reject</v-btn>
    </v-item-group>

  </client-view>
</template>

<script>
import ClientView from '~/components/ClientView'
import ProjectIdConsumer from '~/mixins/projects-id-consumer'

export default {
  components: {
    ClientView
  },

  mixins: [ProjectIdConsumer],
  data: () => ({
    // Project properties
    project: '',
    id: null,
    approvedStations: [],
    rejectedStations: []
  }),

  computed: {
    // The stationId we currently refer to
    stationId() {
      if (this.$store.state.selectedStation === null) {
        return null
      }
      return this.$store.state.selectedStation.id
    },
    status() {
      if (this.approvedStations.includes(this.stationId)) {
        return 'approved'
      }
      if (this.rejectedStations.includes(this.stationId)) {
        return 'rejected'
      }
      return 'pending'
    }
  },
  created() {
    this.getProject(this.$route.params['id']).then(response => {
      this.project = response
      this.id = response.id
      this.approvedStations = response['approvedBy'].map(station => station.id)
      this.rejectedStations = response['rejectedBy'].map(station => station.id)
    })
  },
  methods: {
    submitApproval(approved) {
      const approval = {
        stationId: this.stationId,
        projectId: this.id,
        approved: approved
      }
      this.$axios.post('/approvals', approval).then(response => {
        if (approved) {
          alert('The Project has been approved successfully')
        } else {
          alert('The Project has been rejected successfully')
        }
        this.$router.replace('/projects/' + this.status)
      })
    }
  }
}
</script>
