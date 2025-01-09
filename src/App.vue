<template>
  <form @submit="submitFunction">
    <label for="usename">Pseudo</label>
    <input type="text" id="username" v-model="usernameValue" />
    <p v-if="usernameError">{{ usernameError }}</p>
    <label for="email">Email</label>
    <input type="text" id="email" v-model="emailValue" />
    <p v-if="emailError">{{ emailError }}</p>
    <label for="password">Mot de passe</label>
    <input type="password" id="password" v-model="passwordValue" />
    <label for="confirmPassword">Confirmation du mot de passe</label>
    <input type="password" id="confirmPassword" v-model="verifyPasswordValue" />
    <p v-if="validatePasswordError">{{ validatePasswordError }}</p>
    <button :disabled="isSubmitting">Submit</button>
  </form>
</template>

<script setup>
import { toTypedSchema } from "@vee-validate/zod";
import { jwtDecode } from "jwt-decode";
import { useField, useForm } from "vee-validate";
import { z } from "zod";

const schema = z
  .object({
    username: z.string().min(3, { message: "Trop court !" }).max(10, {
      message: "Trop long !",
    }),
    email: z.string().email({ message: "Email non valide" }),
    password: z.string(),
    verifyPassword: z.string(),
  })
  .refine((data) => data.password === data.verifyPassword, {
    message: "Les mots de passe ne correspondent pas",
    path: ["verifyPassword"],
  });

const { handleSubmit, isSubmitting, resetForm } = useForm({
  validationSchema: toTypedSchema(schema),
  initialValues: {
    username: "",
    email: "",
  },
});

const submitFunction = handleSubmit(async (values) => {
  // console.log(values);
  try {
    const response = await fetch("http://localhost:5000/api/register", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(values),
    });
    const data = await response.json();
    if (response.ok) {
      const decodedToken = jwtDecode(data);
      console.log({ decodedToken });
      resetForm();
    }
  } catch (error) {
    console.log(error);
  }
});

const { value: usernameValue, errorMessage: usernameError } =
  useField("username");

const { value: emailValue, errorMessage: emailError } = useField("email");

const { value: passwordValue } = useField("password");
const { value: verifyPasswordValue, errorMessage: validatePasswordError } =
  useField("verifyPassword");
</script>

<style scoped>
form {
  margin: 20px;
  display: flex;
  flex-direction: column;
}
input {
  width: 300px;
  margin-bottom: 15px;
}
p {
  color: red;
}

button {
  width: 300px;
  padding: 6px 12px;
}
</style>
