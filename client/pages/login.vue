<script setup>
const router = useRouter();
const config = useRuntimeConfig();

const loginFormData = ref({
    email: "",
    password: "",
});

const userName = inject("userName");

onMounted(() => {
    if (userName && userName.value.length > 0) router.push("/");
});

const handleInputChange = (event) => {
    const value = event.target.value;
    loginFormData.value[event.target.id] = value;
};

const looksLikeMail = (str) => {
    const lastAtPos = str.lastIndexOf("@");
    const lastDotPos = str.lastIndexOf(".");
    return (
        lastAtPos < lastDotPos &&
        lastAtPos > 0 &&
        str.indexOf("@@") == -1 &&
        lastDotPos > 2 &&
        str.length - lastDotPos > 2
    );
};

const submitLogin = () => {
    const tempData = Object.assign({}, loginFormData.value);
    if (!looksLikeMail(tempData.email)) alert("Please enter valid email");
    else if (tempData.password.length < 6) alert("Please enter atleast 6 chracters long password");
    else {
        fetch(config.public.apiUrl + "/login", {
            method: "POST",
            credentials: "include",
            headers: {
                Accept: "application/json",
                "Content-Type": "application/json",
            },
            body: JSON.stringify({
                email: tempData.email,
                password: tempData.password,
            }),
        })
            .then((res) => res.json())
            .then((data) => {
                if (data.userName && data.userName.length !== 0) {
                    userName.value = data.userName;
                    router.back();
                } else {
                    console.log(data);
                    alert("Inavlid credentials");
                }
            });
    }
};
</script>
<template>
    <div>
        <div class="flex flex-wrap px-5 pt-5 pb-2 font-medium text-base">
            <div class="p-2 w-full sm:w-1/2">
                <FormInputLabel label-text="Email" label-for="email" />
                <FormInputField
                    input-type="email"
                    v-on:inputChange="handleInputChange"
                    input-id="email"
                    :input-value="loginFormData.email"
                    input-placeholder="Email"
                />
            </div>
            <div class="p-2 w-full sm:w-1/2">
                <FormInputLabel label-text="Password" label-for="password" />
                <FormInputField
                    input-type="password"
                    v-on:inputChange="handleInputChange"
                    input-id="password"
                    :input-value="loginFormData.password"
                    input-placeholder="Password"
                />
            </div>
        </div>

        <div class="px-7">
            <FormButton btn-color="green" btn-text="Login" :btn-click="submitLogin" />

            <div class="mt-2">
                Don't have an account?
                <NuxtLink
                    to="/register"
                    class="underline decoration-orange-600 decoration-2 hover:bg-orange-600 hover:text-white"
                >
                    Signup
                </NuxtLink>
            </div>
        </div>
    </div>
</template>
