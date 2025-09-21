<script setup>
const name = ref('') //ถ้าไม่เอาrefครอป มันจะไม่ถูกrenderออกมา
const email = ref('')
const phone = ref('')
const password = ref('')

const HandelRegister = () => {
    fetch('http://localhost:4000/user',{
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        //payload data
        body: JSON.stringify({ 
            name: name.value,
            email: email.value,
            phone: phone.value,
            password: password.value
        })
    }).then((res) => {
        if (res.ok) {
            alert("Register Success!")
        }
    })
}
</script>

<template>
    <div class="flex h-screen w-screen bg-red-300/50">
        <div class="m-auto flex w-[80vw] h-[80vh] bg-red-500">
            <div class="flex flex-col justify-end w-1/2 h-full bg-red-300">
                <AppLogo class="w-[250px]"/>
            </div>
            <div class="w-1/2 p-[40px] h-full bg-white">
                <div class="flex flex-col gap-6">
                    <div class="flex flex-col">
                        <label for="name" class="text-black">Name : {{ name }}</label>
                        <UInput v-model="name" :ui="{base: 'bg-white'}" id="name" label="Name" type="text" />
                    </div>
                    <div class="flex flex-col">
                        <label for="email" class="text-black">Email : {{ email }}</label>
                        <UInput v-model="email" :ui="{base: 'bg-white'}" id="email" label="Email" type="email" />
                    </div>
                    <div class="flex flex-col">
                        <label for="phone" class="text-black">Phone : {{ phone }}</label>
                        <UInput v-model="phone" :ui="{base: 'bg-white'}" id="phone" label="Phone" type="tel" />
                    </div>
                    <div class="flex flex-col">
                        <label for="password" class="text-black">Password : {{ password }}</label>
                        <UInput v-model="password" :ui="{base: 'bg-white'}" id="password" label="Password" type="password" />
                    </div>
                    <div class="flex flex-col">
                        <UButton label="Register" @click="HandelRegister()" class="cursor-pointer "/>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>