<!-- <script lang="ts" context="module">
    export type Week = {
        yearNo: number
        yearId: number
        weekNo: number
        weekId: number
    }
</script> -->

<script lang="ts">
    import { page } from "$app/stores"
    import type { CalendarConfig } from "../+page.svelte"

    /* ========================================================================================== */

    const config: CalendarConfig = {
        name: $page.url.searchParams.get("name") || "",
        birthday: $page.url.searchParams.get("birthday") || "",
        lifeExpectancy: Number($page.url.searchParams.get("lifeExpectancy")),
    }

    const years = [...Array(config.lifeExpectancy + 1).keys()]
    // years.shift() // start at 1

    const weeksLived = Math.floor(
        (new Date().getTime() - new Date(config.birthday).getTime()) / 604800000,
    )

    /* ========================================================================================== */

    function colorWeek(year: number, week: number): string {
        const weeks = year * 52 + week

        if (weeks < weeksLived) return "bg-green-000 border border-green-050"
        // if (year >= 65) return "border border-step-300 bg-step-100"
        return "bg-step-100 border border-step-150"
    }
</script>

<div class="flex flex-col items-center gap-6 p-6">
    <!-- <div class="flex flex-col items-center py-6">
        <a href="/" class="link font-mono text-3xl text-black dark:text-white">lifetime</a>
    </div> -->
    <!-- {JSON.stringify(config)} -->
    <div class="card flex flex-col gap-6 p-6 pt-8">
        <div class="flex flex-col items-center gap-4">
            <h1 class="text-4xl font-semibold">{config.name}</h1>
            <p class="text-step-500 flex gap-2">
                <span>Birthday: {config.birthday}</span>
                <span>–</span>
                <span>Life Expectancy: {config.lifeExpectancy}</span>
            </p>
        </div>
        <hr />
        <div>
            {#each years as year}
                <div class="flex h-4 w-full items-center gap-1">
                    <p class="text-step-400 w-4 text-right font-mono text-xs">
                        {year % 5 === 0 ? year : ""}
                    </p>
                    {#each [...Array(52).keys()] as week}
                        <div class="{colorWeek(year, week)} size-3 rounded-sm"></div>
                    {/each}
                    <p class="text-step-400 w-4 text-right font-mono text-xs">
                        {year % 5 === 0 ? new Date(config.birthday).getFullYear() + year : ""}
                    </p>
                </div>
            {/each}
        </div>
        <hr />
        <div class="flex flex-col items-center gap-2">
            <p class="text-step-700 [&>span]:font-medium [&>span]:text-white">
                <span>{weeksLived}</span> of your <span>{config.lifeExpectancy * 52}</span> weeks
                have already passed (<span
                    >{Math.round((weeksLived / (config.lifeExpectancy * 52)) * 10000) / 100}%</span
                >).
            </p>
            <p class="text-step-700 [&>span]:font-medium [&>span]:text-white">
                Your adult life (18–{config.lifeExpectancy} years) is
                <span>{(config.lifeExpectancy - 18) * 52}</span>
                weeks long, of those
                <span>{weeksLived - 18 * 52}</span>
                have passed (<span
                    >{Math.round(
                        ((weeksLived - 18 * 52) / ((config.lifeExpectancy - 18) * 52)) * 10000,
                    ) / 100}%</span
                >).
            </p>
        </div>
        <div class="text-step-500 flex justify-center gap-2 text-sm">
            <a href="/" class="link text-step-500">lifetime.timephy.com</a>
        </div>
    </div>
</div>
