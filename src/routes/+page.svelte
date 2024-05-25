<script lang="ts" context="module">
    export type CalendarConfig = {
        /** Name of person */
        name: string
        /** A date with the format "YYYY-MM-DD" */
        birthday: string
        /** The length of life in years. */
        lifeExpectancy: number
    }
</script>

<script lang="ts">
    import { goto } from "$app/navigation"

    let form: CalendarConfig = {
        name: "",
        birthday: "",
        lifeExpectancy: 0,
    }

    function submit() {
        // check form fields format
        if (form.name === "" || form.birthday === "") {
            alert("Please fill in all fields.")
            return
        }
        if (form.lifeExpectancy <= 19) {
            alert("Life expectancy must be greater than 19.")
            return
        }
        if (form.lifeExpectancy >= 200) {
            alert("Life expectancy must be less than 200.")
            return
        }
        if (form.birthday > new Date().toISOString().split("T")[0]) {
            alert("Birthday must be in the past.")
            return
        }

        // `as any` to allow `number` type of `lifeExpectancy` instead of `string`
        goto(`/calendar?${new URLSearchParams(form as any).toString()}`)
    }
</script>

<div class="flex h-full flex-col items-center justify-center p-6">
    <div class="card flex flex-col gap-6 p-6">
        <!-- {JSON.stringify(form)} -->
        <div class="flex flex-col items-center gap-2 px-4 pt-2">
            <h1 class="font-mono text-3xl font-semibold">lifetime</h1>
            <p class="text-step-500 text-sm">A calendar outlining your weeks to live.</p>
        </div>
        <hr />
        <div class="flex flex-col gap-4">
            <div class="flex justify-between gap-8">
                <p>Your Name</p>
                <input
                    type="text"
                    class="border-step-150 bg-step-100 outline-blue-000 w-auto rounded-xl border px-3 py-0.5 text-right"
                    bind:value={form.name}
                />
            </div>
            <div class="flex justify-between gap-8">
                <p>Birthday</p>
                <input
                    type="date"
                    class="border-step-150 bg-step-100 outline-blue-000 w-auto rounded-xl border px-3 py-0.5 text-right"
                    bind:value={form.birthday}
                />
            </div>
            <div class="flex justify-between gap-8">
                <p>Life Expectancy <span class="text-step-500">(years)</span></p>
                <input
                    type="number"
                    class="border-step-150 bg-step-100 outline-blue-000 w-20 rounded-xl border px-3 py-0.5 text-right"
                    bind:value={form.lifeExpectancy}
                />
            </div>
        </div>
        <hr />
        <button class="btn btn-blue" on:click={submit}>Create Calendar</button>
    </div>
</div>
