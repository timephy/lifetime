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

    let error: string | null = null

    function submit() {
        error = null
        // check form fields format
        if (form.name === "" || form.birthday === "") {
            error = "Please fill in all fields."
            return
        }
        if (form.lifeExpectancy <= 19) {
            error = "Life expectancy must be greater than 19."
            return
        }
        if (form.lifeExpectancy >= 200) {
            error = "Life expectancy must be less than 200."
            return
        }
        if (form.birthday > new Date().toISOString().split("T")[0]) {
            error = "Birthday must be in the past."
            return
        }

        // NOTE: `as any` to allow `number` type of `lifeExpectancy` instead of `string`
        goto(`/calendar?${new URLSearchParams(form as any).toString()}`)
    }
</script>

<div class="flex h-full flex-col items-center justify-center p-6">
    <form class="card flex flex-col gap-6 p-6" onsubmit={submit} method="dialog">
        <!-- {JSON.stringify(form)} -->
        <div class="flex flex-col items-center gap-2 px-4 pt-2">
            <h1 class="font-mono text-3xl font-semibold">lifetime</h1>
            <p class="text-sm text-step-500">A calendar outlining your weeks to live.</p>
        </div>
        <hr />
        <div class="flex flex-col gap-4">
            <div class="flex justify-between gap-8">
                <p>Your Name</p>
                <input
                    type="text"
                    class="w-auto rounded-xl border border-step-150 bg-step-100 px-3 py-0.5 text-right outline-blue-000"
                    bind:value={form.name}
                />
            </div>
            <div class="flex justify-between gap-8">
                <p>Birthday</p>
                <input
                    type="date"
                    class="w-auto rounded-xl border border-step-150 bg-step-100 px-3 py-0.5 text-right outline-blue-000"
                    bind:value={form.birthday}
                />
            </div>
            <div class="flex justify-between gap-8">
                <p>Life Expectancy <span class="text-step-500">(years)</span></p>
                <input
                    type="number"
                    class="w-20 rounded-xl border border-step-150 bg-step-100 px-3 py-0.5 text-right outline-blue-000"
                    bind:value={form.lifeExpectancy}
                />
            </div>
            {#if error}
                <p class="text-center text-xs text-red-000">{error}</p>
            {/if}
        </div>
        <hr />
        <button class="btn btn-blue">Create Calendar</button>
    </form>
</div>
