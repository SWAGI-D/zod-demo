<template>
  <form @submit.prevent="onSubmit">
    <div>
      <label for="name">Name:</label>
      <input type="text" id="name" name="name" v-model="formData.name" required/>
      <div v-if="errors?.name">
        <span v-for="error in errors.name._errors" :key="error">
          {{  error }}
        </span>
      </div>
    </div>
    <div>
      <label for="age">Age:</label>
      <input type="number" id="age" name="age" v-model="formData.age" required/>
      <div v-if="errors?.age">
        <span v-for="error in errors.age._errors" :key="error">
          {{  error }}
        </span>
      </div>
    </div>
    <div>
      <label for="email">Email:</label>
      <input type="text" id="email" name="email" v-model="formData.email" required/>
      <div v-if="errors?.email">
        <span v-for="error in errors.email._errors" :key="error">
          {{  error }}
        </span>
      </div>
    </div>
    <div>
      <label for="password">Password:</label>
      <input type="password" id="password" name="password" v-model="formData.password"/>
      <div v-if="errors?.password">
        <span v-for="error in errors.password._errors" :key="error">
          {{  error }}
        </span>
      </div>
    </div>
    <div>
      <label for="confirmPassword">Confirm Password:</label>
      <input type="password" id="confirmPassword" name="confirmPassword" v-model="formData.confirmPassword"/>
    </div>
    <div v-if="errors?.confirmPassword">
        <span v-for="error in errors.confirmPassword._errors" :key="error">
          {{  error }}
        </span>
      </div>
    <button type="submit">Submit</button>
  </form>
</template>

<script setup lang="ts">
import { z } from 'zod';
import {ref} from 'vue';


const EmployeeSchema = z.object({
  name: z.string().min(3, {message: "Name should contain at least 3 letters"}),
  age: z.number({message: "Age should only be a number"}).gt(10, {message: "Age should be greater than 10"}),
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

function onSubmit()
{
  const validSchema = EmployeeSchema.safeParse(formData.value)

  if(!validSchema.success)
  {
    errors.value = validSchema.error.format()
  }

  else
  {
    errors.value = null
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

