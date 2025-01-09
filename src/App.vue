<template>
  <div>
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
  </div>
</template>

<script setup>
import { toTypedSchema } from "@vee-validate/zod";
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

useForm({
  validationSchema: toTypedSchema(schema),
  initialValues: {
    username: "",
    email: "",
  },
});

const { value: usernameValue, errorMessage: usernameError } =
  useField("username");

const { value: emailValue, errorMessage: emailError } = useField("email");

const { value: passwordValue } = useField("password");
const { value: verifyPasswordValue, errorMessage: validatePasswordError } =
  useField("verifyPassword");
</script>

<style scoped>
div {
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
</style>
