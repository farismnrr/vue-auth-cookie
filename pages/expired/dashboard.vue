<script setup lang="ts">
let token: string | null = null;
const accessToken = process.client ? localStorage.getItem("accessToken") : null;
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";

const responseData = ref({ data: { trial: { id: "", email: "", free_trial: false } } });
function getSecureCookie(id: string) {
    const cookieString = document.cookie;
    const regex = new RegExp(`${id}=([^;]*)`, "i");
    const match = regex.exec(cookieString);

    if (match) {
        return decodeURIComponent(match[1]);
    }

    return null;
}

async function getAccessToken() {
    token = getSecureCookie("jwt");
    if (!token) {
        navigateTo("/expired");
        return;
    }
    const refreshToken = JSON.parse(token).status;
    try {
        const response = await $fetch(`http://localhost:5500/api/v1/users/auth`, {
            method: "PUT",
            body: JSON.stringify({
                refresh_token: refreshToken,
            }),
        });
        const responseData = response as {
            data: { access_token: string };
        };
        localStorage.setItem("accessToken", responseData.data.access_token);
        token = responseData.data.access_token;
        setTimeout(getAccessToken, 30 * 60 * 1000);
        navigateTo("/expired/dashboard");
    } catch (error: any) {
        console.error("Error fetching data:", error);
        navigateTo("/expired");
    }
}

async function logoutUser() {
    token = getSecureCookie("jwt");
    if (!token) {
        navigateTo("/expired");
        return;
    }
    const refreshToken = JSON.parse(token).status;
    try {
        await $fetch(`http://localhost:5500/api/v1/users/auth`, {
            method: "DELETE",
            body: JSON.stringify({
                refresh_token: refreshToken,
            }),
        });
        toast.success("Logout successfully");
        navigateTo("/expired");
    } catch (error: any) {
        console.error("Error fetching data:", error);
        toast.error("Failed to logout");
    }
}

async function fetchData() {
    try {
        const response = await $fetch(`http://localhost:5500/api/v1/trials`, {
            method: "GET",
            headers: {
                Authorization: `Bearer ${accessToken}`,
            },
        });
        responseData.value = response as {
            data: { trial: { id: string; email: string; free_trial: boolean } };
        };
        console.log("Trial fetched successfully:", responseData.value.data.trial);
    } catch (error: any) {
        getAccessToken();
    }
}

async function logoutClick() {
    await logoutUser();
    localStorage.removeItem("accessToken");
    navigateTo("/expired");
};

fetchData();
</script>

<template>
    <div>
        <h1>Expired</h1>
        <p>This is a expired page</p>
        <p>Access Token: {{ accessToken }}</p>
        <p>Trial fetched successfully: {{ responseData.data.trial }}</p>
    </div>
    <div>
        <button @click.prevent="logoutClick">Logout</button>
    </div>
</template>
