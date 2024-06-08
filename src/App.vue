<template>
  <VForm @submit.prevent="onSubmit">
    <div>
      <VTextField label="Name" type="text" id="name" name="name" v-model="formData.name" :error-messages="validationErrors.name"/>
    </div>
    <div>
      <VNumberInput label="Age" type="number" id="age" name="age" v-model="formData.age" :error-messages="validationErrors.age" required/>
    </div>
    <div>
      <VTextField label="Email" type="email" id="email" name="email" v-model="formData.email" :error-messages="validationErrors.email"/>
    </div>
    <div>
      <VTextField label="Password" type="password" id="password" name="password" v-model="formData.password" :error-messages="validationErrors.password"/>
    </div>
    <div>
      <VTextField label="Confirm Password" type="password" id="confirmPassword" name="confirmPassword" v-model="formData.confirmPassword" :error-messages="validationErrors.confirmPassword"/>
    </div>
    <button type="submit">Submit</button>
  </VForm>
</template>

<script setup lang="ts">
import { z } from 'zod';
import {ref, reactive} from 'vue';
import {VForm, VTextField} from 'vuetify/components'
import { VNumberInput } from 'vuetify/lib/labs/components.mjs';

const errorMessage = ref('')

const EmployeeSchema = z.object({
  name: z.string().min(3, {message: "Name should contain at least 3 letters"}),
  age: z.number().gt(10, {message: "Age should be greater than 10"}),
  email: z.string().email({message: "Please enter a valid email"}),
  password: z.string().min(8, "Password should contain at least 8 characters"),
  confirmPassword: z.string().min(8, "Password should contain at least 8 characters"),
}).refine((data) => data.password === data.confirmPassword, {
        message: "Passwords should match",
        path: ["confirmPassword"],
    });

type Employee = z.infer<typeof EmployeeSchema>

const errors = ref<z.ZodFormattedError<Employee> | null>(null)

const formData = ref({
  name: "",
  age: 0,
  email: "",
  password: "",
  confirmPassword: ""
})

const validationErrors = reactive<
    Record<keyof Employee, string[]>
  >({
    name: [],
    age: [],
    email: [],
    password: [],
    confirmPassword: []

  })

function validateForm()
{
  const validSchema = EmployeeSchema.safeParse(formData.value)

  if (!validSchema.success) {
      validSchema.error.errors.forEach((err) => {
        validationErrors[err.path[0] as keyof Employee] = [
          err.message,
        ]
      })
      return false
    }
    return true
}

function onSubmit()
{
  try{
    validateForm()
  }

  catch(error: any)
  {
    errorMessage.value = error.message || 'An error occurred'
  }
}

</script>

<style>
form {
  max-width: 400px;
  margin: 0 auto;
}

div {
  margin-bottom: 10px;
}

label {
  display: block;
  font-weight: bold;
}

input[type="text"],
input[type="number"],
input[type="date"],
select {
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

span {
  color: red;
}

button {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}
</style>
