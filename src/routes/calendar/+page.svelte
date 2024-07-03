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
    import { onMount } from "svelte"
    import type { CalendarConfig } from "../+page.svelte"

    /* ========================================================================================== */

    let config: CalendarConfig = $state({
        name: "Default",
        birthday: "2000-01-01",
        lifeExpectancy: 85,
    })

    const years = $derived([...Array(config.lifeExpectancy + 1).keys()])

    const weeksLived = $derived(
        Math.floor(
            (new Date().getTime() -
                new Date(new Date(config.birthday).getFullYear(), 0, 1).getTime()) /
                604800000,
        ),
    )

    onMount(() => {
        config = {
            name: $page.url.searchParams.get("name") || "",
            birthday: $page.url.searchParams.get("birthday") || "",
            lifeExpectancy: Number($page.url.searchParams.get("lifeExpectancy")),
        }
    })

    /* ========================================================================================== */

    function colorWeek(year: number, week: number): string {
        const weeks = year * 52 + week

        if (weeks < weeksLived) return "week week-green"
        if (weeks == weeksLived) return "week week-blue"
        // if (year >= 65) return "border border-step-300 bg-step-100"
        return "week week-gray"
    }
</script>

<div class="flex flex-col items-center gap-6 screen:p-6">
    <!-- {JSON.stringify(config)} -->
    <div class="flex w-full flex-col gap-4 p-6 pt-8 screen:card screen:max-w-[50rem]">
        <div class="flex flex-col items-center gap-2">
            <h1 class="text-4xl font-semibold">{config.name}</h1>
            <p class="flex gap-2 text-step-500">
                <span>Birthday: {config.birthday}</span>
                <span>–</span>
                <span>Life Expectancy: {config.lifeExpectancy}</span>
            </p>
        </div>
        <!-- <hr /> -->
        <div class="calendar">
            {#each years as year}
                <div class="year">
                    <div class="text">
                        <p>
                            {year % 5 === 0 ? year : ""}
                        </p>
                    </div>
                    {#each [...Array(52).keys()] as week}
                        <div class={colorWeek(year, week)}></div>
                    {/each}
                    <div class="text">
                        <p>
                            {year % 5 === 0 ? new Date(config.birthday).getFullYear() + year : ""}
                        </p>
                    </div>
                </div>
            {/each}
        </div>
        <!-- <hr /> -->
        <div class="flex flex-col items-center gap-1">
            <p
                class="text-step-700 [&>span]:font-medium [&>span]:text-black [&>span]:dark:text-white"
            >
                <span>{weeksLived}</span> of your <span>{config.lifeExpectancy * 52}</span> weeks
                have already passed (<span
                    >{Math.round((weeksLived / (config.lifeExpectancy * 52)) * 10000) / 100}%</span
                >).
            </p>
            <p
                class="text-step-700 [&>span]:font-medium [&>span]:text-black [&>span]:dark:text-white"
            >
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
        <div class="text-tiny flex justify-center gap-2 text-step-500">
            <a href="/" class="link text-step-500">lifetime.timephy.com</a>
        </div>
    </div>
</div>

<style lang="postcss">
    .text-normal {
        @apply print:text-xs;
    }
    .text-tiny {
        @apply text-sm print:text-[0.5rem];
    }

    p {
        @apply text-normal;
    }

    .calendar {
        --gap: 3px;
        @media print {
            --gap: 2px;
        }

        @apply flex w-full flex-col gap-[var(--gap)];

        .year {
            @apply flex w-full items-center justify-center gap-[var(--gap)];

            --sides: 16px;
            & > .text {
                @apply h-0 w-[var(--sides)] shrink-0 text-right font-mono text-xs text-step-400 print:text-[0.5rem];
                @apply flex items-center;

                &:first-child {
                    @apply justify-end;
                }
                &:last-child {
                    @apply justify-start;
                }
            }

            --w-week: calc((100% - 2 * var(--sides)) / 52);
            @media print {
                --w-week: calc((100% - 2 * var(--sides)) / 52 * 0.6);
            }

            .week {
                @apply aspect-square w-[var(--w-week)] rounded-sm;
            }
            .week-gray {
                @apply border border-step-150 bg-step-100;
                @apply print:border-step-350 print:bg-step-300;
            }
            .week-green {
                @apply border border-green-050 bg-green-000;
            }
            .week-red {
                @apply border border-red-050 bg-red-000;
            }
            .week-orange {
                @apply border border-orange-050 bg-orange-000;
            }
            .week-blue {
                @apply border border-blue-050 bg-blue-000;
            }
        }
    }
</style>
