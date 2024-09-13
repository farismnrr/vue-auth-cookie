<script setup lang="ts">
const token = "YOUR_SECURE_TOKEN";

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

function getSecureCookie(id: string) {
    const cookieString = document.cookie;
    const regex = new RegExp(`${id}=([^;]*)`, "i");
    const match = regex.exec(cookieString);

    if (match) {
        return decodeURIComponent(match[1]);
    }

    return null;
}

let getToken: string | null = null;
if (typeof document !== 'undefined') {
    getToken = getSecureCookie("jwt");
    if (!getToken) {
        navigateTo("/");
    }
}

const handleClick = async () => {
    setSecureCookie("jwt", token);
    navigateTo("/secure/dashboard");
};
</script>

<template>
    <div>
        <h2>Unsecure Page</h2>
        <p>Token: {{ getToken }}</p>
        <button @click.prevent="handleClick">Log In</button>
    </div>
    <div>
        <button @click="navigateTo('/')">Home</button>
    </div>
</template>
