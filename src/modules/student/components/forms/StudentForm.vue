<script>
import * as yup from 'yup'
import StudentService from '../../services'
import cpfValidator from '@/utils/cpfValidator.js'
import { STUDENT_LIST } from '@/constants/router'

export default {
  name: 'StudentForm',
  data() {
    return {
      show: false,
      formData: {
        id: '',
        ra: '',
        name: '',
        email: '',
        cpf: ''
      },
      validationSchema: yup.object().shape({
        name: yup.string().required('Name is required'),
        email: yup.string().email('Invalid email address').required('Email is required'),
        cpf: yup
          .string()
          .required('CPF is required')
          .test('cpf-validation', 'Invalid CPF', cpfValidator)
      }),
      validationErrors: {},
      loading: false,
      getStudentPath: STUDENT_LIST.path,
      disableCPF: false
    }
  },
  methods: {
    async submitForm() {
      try {
        await this.validationSchema.validate(this.formData, { abortEarly: false })
        await StudentService.createStudent(this.formData)
        this.$router.push(STUDENT_LIST.path)
      } catch (error) {
        if (error.name === 'ValidationError') {
          this.handleValidationErrors(error.errors)
        } else {
          this.handleLoginError(error)
        }
      }
    },
    handleValidationErrors(errors) {
      const errorMessage = errors.join(' - ')

      this.$snackbar.showSnackbar(errorMessage)
    },
    handleLoginError(error) {
      const errorMessage = error.response
        ? `Error: ${error.response.status} - ${error.response.data.message}`
        : 'Unknown error.'

      this.$snackbar.showSnackbar(errorMessage)
    },
    goToList() {
      sessionStorage.removeItem('student')
      this.$router.push(STUDENT_LIST.path)
    }
  },
  created() {
    const studentData = sessionStorage.getItem('student')

    if (studentData) {
      const parsedData = JSON.parse(studentData)
      this.formData.id = parsedData.id || ''
      this.formData.ra = parsedData.ra || ''
      this.formData.name = parsedData.name || ''
      this.formData.email = parsedData.email || ''
      this.formData.cpf = parsedData.cpf || ''
      this.show = true
      this.disableCPF = true
    }
  }
}
</script>

<template>
  <div class="container">
    <v-card class="mr-3 ml-3 mt-3">
      <v-toolbar density="compact" >
        <v-toolbar-title>Student Form</v-toolbar-title>
      </v-toolbar>
      <v-card-text>
        <v-form @submit.prevent="submitForm">
          <v-row>
            <v-col cols="2" v-if="show">
              <v-text-field
                disabled="true"
                v-model="formData.id"
                label="ID"
                variant="outlined"
              ></v-text-field>
            </v-col>
            <v-col cols="5" v-if="show">
              <v-text-field
                disabled="true"
                v-model="formData.ra"
                label="RA"
                variant="outlined"
              ></v-text-field>
            </v-col>
            <v-col cols="5">
              <v-text-field
                :disabled="disableCPF"
                v-model="formData.cpf"
                label="CPF"
                variant="outlined"
                v-mask="'###.###.###-##'"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <v-text-field v-model="formData.name" label="Name" variant="outlined"></v-text-field>
            </v-col>
            <v-col>
              <v-text-field
                v-model="formData.email"
                label="E-mail"
                variant="outlined"
              ></v-text-field>
            </v-col>
          </v-row>
        </v-form>
      </v-card-text>
      <!-- <v-divider></v-divider> -->
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn type="submit" variant="tonal" color="green-darken-4" class="ma-1"> Save </v-btn>
        <v-btn @click="goToList()" variant="tonal" color="red-darken-4" class="ma-1"> Close </v-btn>
      </v-card-actions>
    </v-card>
  </div>
</template>
