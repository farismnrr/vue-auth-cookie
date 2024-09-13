<script setup lang="ts">
function getCookie(id: string) {
    let value = document.cookie.match("(^|;)?" + id + "=([^;]*)(;|$)");
    return value ? unescape(value[2]) : null;
}

const token = getCookie("token");

const logoutClick = async () => {
    window.location.href = "/unsecure";
};
</script>

<template>
    <div>
        <h1>Dashboard</h1>
        <p>This is a secure page</p>
        <p>Token: {{ token }}</p>
    </div>

    <div>
        <button @click.prevent="logoutClick">Log Out</button>
    </div>
</template>
