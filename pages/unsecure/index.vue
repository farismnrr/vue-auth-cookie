<script setup lang="ts">
function setCookie(id: string, value: string) {
    document.cookie = id + "=" + value;
}
const token = "YOUR_UNSECURE_TOKEN";

const handleClick = async () => {
    setCookie("token", token);
    navigateTo("/unsecure/dashboard");
};
</script>

<template>
    <div>
        <button @click.prevent="handleClick">Log In</button>
    </div>
    <div>
        <button @click="navigateTo('/')">Home</button>
    </div>
</template>
