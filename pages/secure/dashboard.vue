<script setup lang="ts">
function getSecureCookie(id: string) {
    const cookieString = document.cookie;
    const regex = new RegExp(`${id}=([^;]*)`, "i");
    const match = regex.exec(cookieString);

    if (match) {
        return decodeURIComponent(match[1]);
    }

    return null;
}

function deleteSecureCookie(id: string) {
    const cookieOptions = {
        secure: true,
        httpOnly: true,
        sameSite: "strict",
        expires: new Date(Date.now() - 1000), // set expiration date to the past
    };

    let cookieString = `${id}=;`;

    Object.keys(cookieOptions).forEach((option) => {
        cookieString += `${option}=${cookieOptions[option as keyof typeof cookieOptions]};`;
    });

    document.cookie = cookieString;
}

let token: string | null = null;
if (typeof document !== "undefined") {
    token = getSecureCookie("jwt");
    if (!token) {
        navigateTo("/");
    }
}

const logoutClick = async () => {
    deleteSecureCookie("jwt");
    window.location.href = "/secure";
};
</script>

<template>
    <div>
        <h1>Dashboard</h1>
        <p>This is a secure page</p>
        <p>Token: {{ token }}</p>
    </div>
    <div>
        <button @click.prevent="logoutClick">Logout</button>
    </div>
</template>
