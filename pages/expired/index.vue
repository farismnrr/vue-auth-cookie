<script setup lang="ts">
const email = ref("");
const password = ref("");
const refreshToken = ref("");
const errorMessage = ref("");

function setSecureCookie(id: string, value: string) {
    const cookieOptions = {
        secure: true,
        httpOnly: true,
        sameSite: "strict",
        expires: new Date(Date.now() + 31536000000), // 1 year
    };

    let cookieString = `${id}=${value};`;

    Object.keys(cookieOptions).forEach((option) => {
        cookieString += `${option}=${cookieOptions[option as keyof typeof cookieOptions]};`;
    });

    document.cookie = cookieString;
}

async function handleSubmit() {
    try {
        const response = await $fetch(`http://localhost:5500/api/v1/users/login`, {
            method: "POST",
            body: JSON.stringify({
                email: email.value,
                password: password.value,
            }),
        });

        const responseData = response as { data: { refresh_token: string; access_token: string } };
        refreshToken.value = JSON.stringify({ status: responseData.data.refresh_token });
        localStorage.setItem("accessToken", responseData.data.access_token);

        setSecureCookie("jwt", refreshToken.value);
        navigateTo("/expired/dashboard");
    } catch (error: any) {
        const responseMessage = error.response;
        errorMessage.value = JSON.stringify(responseMessage._data.errors, null, 2);
    }
}
</script>

<template>
    <div>
        <h1>Log In Form</h1>
        <form @submit.prevent="handleSubmit">
            <input type="email" v-model="email" placeholder="Email" required />
            <input type="password" v-model="password" placeholder="Password" required />
            <button @click.prevent="handleSubmit">Log In</button>
        </form>
        <p v-if="errorMessage">{{ errorMessage }}</p>
    </div>
</template>
