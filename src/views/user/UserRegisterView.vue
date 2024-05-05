<template>
  <div id="userRegisterView">
    <h2 style="margin-bottom: 16px">用户注册</h2>
    <a-form
      style="max-width: 480px; margin: 0 auto"
      ref="formRef"
      :rules="rules"
      :model="form"
      label-align="left"
      auto-label-width
      @submit="handleSubmit"
    >
      <a-form-item field="userAccount" label="账号" validate-trigger="blur">
        <a-input v-model="form.userAccount" placeholder="请输入账号" />
      </a-form-item>
      <a-form-item field="userPassword" label="密码" validate-trigger="blur">
        <a-input-password
          v-model="form.userPassword"
          placeholder="请输入密码"
        />
      </a-form-item>
      <a-form-item
        field="userPassword2"
        label="确认密码"
        validate-trigger="blur"
      >
        <a-input-password
          v-model="form.checkPassword"
          placeholder="请确认密码"
        />
      </a-form-item>
      <a-form-item>
        <a-space>
          <a-button type="primary" html-type="submit">提交</a-button>
          <a-button @click="$refs.formRef.resetFields()">重置</a-button>
        </a-space>
      </a-form-item>
    </a-form>
  </div>
</template>

<script setup lang="ts">
import { reactive } from "vue";
import { UserControllerService, UserRegisterRequest } from "../../../generate";
import message from "@arco-design/web-vue/es/message";
import { useStore } from "vuex";
import { useRouter } from "vue-router";

const store = useStore();
const router = useRouter();

const handleSubmit = async ({ values, errors }) => {
  const res = await UserControllerService.userRegisterUsingPost(form);
  if (res.code === 0) {
    await store.dispatch("user/getLoginUser");
    router.push({
      path: "/user/login",
      replace: true,
    });
  } else {
    message.error("注册失败，" + res.message);
  }
  alert(JSON.stringify(form));
};

const form = reactive({
  checkPassword: "",
  userAccount: "",
  userPassword: "",
}) as UserRegisterRequest;

const rules = {
  userAccount: [
    {
      required: true,
      message: "name is required",
    },
  ],
  userPassword: [
    {
      required: true,
      message: "password is required",
    },
  ],
  checkPassword: [
    {
      required: true,
      message: "password is required",
    },
    {
      validator: (value, cb) => {
        if (value !== form.userPassword) {
          cb("two passwords do not match");
        } else {
          cb();
        }
      },
    },
  ],
};
</script>
