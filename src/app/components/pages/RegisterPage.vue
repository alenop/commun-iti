<script setup lang="ts">
import { ElMessage } from "element-plus";
import { reactive } from "vue";
import { useRouter } from "vue-router";
import { UserAPI } from "@/modules/user/services";
import { AuthenticationService } from "@/modules/authentication/services";
import { useProvider } from "@/app/platform";
import type { FormRules, FormInstance } from "element-plus";

const [userApi, authService] = useProvider([UserAPI, AuthenticationService]);
const router = useRouter();

const registerModel = reactive({
  username: "",
  password: "",
  passwordConfirmation: ""
});

const userNameRegex = /^(\w+)$/i;

const registerFormRules = reactive<FormRules>({
  username: [
    {
      required: true,
      message: "Pseudo obligatoire",
      pattern: userNameRegex,
      trigger: "blur"
    }
  ],
  password: [{
    required: true,
    message: "Mdp obligatoire",
    trigger: "blur"

  }],
  passwordConfirmation: [{
    required: true,
    message: "Mdp confirm obligatoire",
    trigger: "blur"
  }]
});

async function onSubmit(form?: FormInstance) {
  if (!form) {
    return;
  }

  try {
    await form.validate();
    if (registerModel.password != registerModel.passwordConfirmation) {
      ElMessage("failed you noted the wrong password in the second in the second input");
      throw new Error("failed bourrin");
    }

    if (await userApi.exists(registerModel.username)) {
      ElMessage("failed you already exists");
      throw new Error("failed you already exists");
    }
    await userApi.register(registerModel);
    router.push("/login");
  } catch (e) {
    return;
  }
}
</script>
<template>
  <div class="register center-children full-h">
    <main class="width-s">
      <h1 class="register-title">Créer un compte</h1>

      <div class="register-form">
        <el-form ref="form" :model="registerModel" :rules="registerFormRules" label-position="top" class="register-form"
          @submit.prevent="onSubmit($refs.form)">
          <el-form-item label="Pseudo" prop="username">
            <el-input v-model="registerModel.username" />
          </el-form-item>

          <el-form-item label="Mot de passe" prop="password">
            <el-input v-model="registerModel.password" type="password" />
          </el-form-item>

          <el-form-item label="Confirmez votre mot de passe" prop="passwordConfirmation">
            <el-input v-model="registerModel.passwordConfirmation" type="password" />
          </el-form-item>

          <el-form-item>
            <div class="form-actions">
              <el-button type="primary" native-type="submit"> Créer mon compte </el-button>

              <router-link to="/login">J'ai déjà un compte</router-link>
            </div>
          </el-form-item>
        </el-form>
      </div>
    </main>
  </div>
</template>
<style scoped lang="scss">
@use "@/app/styles/var";
@use "@/app/styles/mixins";
@use "sass:list";

.form-actions {
  width: 100%;
  display: flex;
  justify-content: space-between;
}
</style>
