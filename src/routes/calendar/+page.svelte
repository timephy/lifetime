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

    const agesArray = $derived([...Array(config.lifeExpectancy + 1).keys()])

    const DAYS_PER_WEEK = 7
    const WEEKS_PER_YEAR = 52
    // const weeksPassed = $derived(
    //     // ceil to round 0 weeks lived to "a length of 1"
    //     Math.ceil((new Date().getTime() - new Date(config.birthday).getTime()) / MS_PER_WEEK),
    // )

    onMount(() => {
        config = {
            name: $page.url.searchParams.get("name") || "",
            birthday: $page.url.searchParams.get("birthday") || "",
            lifeExpectancy: Number($page.url.searchParams.get("lifeExpectancy")),
        }
    })

    /* ========================================================================================== */

    const now = new Date()

    function classForWeek(year: number, week: number): string {
        const weekStart = new Date(config.birthday)
        weekStart.setFullYear(weekStart.getFullYear() + year)
        weekStart.setDate(weekStart.getDate() + week * DAYS_PER_WEEK)
        let weekEnd = new Date(weekStart)
        weekEnd.setDate(weekEnd.getDate() + DAYS_PER_WEEK)

        // NOTE: 52*7 = 364 days, so the last week of the year is longer than 7 days
        if (week === WEEKS_PER_YEAR - 1) {
            // extend last week until the birthday
            weekEnd = new Date(config.birthday)
            weekEnd.setFullYear(weekEnd.getFullYear() + year + 1)
        }

        // if (weeks < weeksPassed) return "week week-green"
        if (weekStart <= now && now < weekEnd) {
            return "week week-orange screen:animate-[animate-opacity_3s_ease-in-out_infinite]"
        } else if (weekStart < now) {
            // if (year < 18) {
            //     return "week week-blue"
            // }
            return "week week-green"
        }
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
            {#each agesArray as age}
                <div class="year">
                    <div class="text">
                        <p>
                            {age % 5 === 0 ? age : ""}
                        </p>
                    </div>

                    <!-- ! WEEKS -->
                    {#each [...Array(WEEKS_PER_YEAR).keys()] as week}
                        <div class={classForWeek(age, week)}></div>
                    {/each}
                    <!-- ! WEEKS -->

                    <div class="text">
                        <p>
                            {age % 5 === 0 ? new Date(config.birthday).getFullYear() + age : ""}
                        </p>
                    </div>
                </div>
            {/each}
        </div>
        <!-- <hr /> -->
        <!-- <div class="flex flex-col items-center gap-1">
            <p
                class="text-step-700 [&>span]:font-medium [&>span]:text-black [&>span]:dark:text-white"
            >
                <span>{weeksPassed}</span> of your <span>{config.lifeExpectancy * WEEKS_PER_YEAR}</span> weeks
                have already passed (<span
                    >{Math.round((weeksPassed / (config.lifeExpectancy * WEEKS_PER_YEAR)) * 10000) / 100}%</span
                >).
            </p>
            <p
                class="text-step-700 [&>span]:font-medium [&>span]:text-black [&>span]:dark:text-white"
            >
                Your adult life (18–{config.lifeExpectancy} years) is
                <span>{(config.lifeExpectancy - 18) * WEEKS_PER_YEAR}</span>
                weeks long, of those
                <span>{weeksPassed - 18 * WEEKS_PER_YEAR}</span>
                have passed (<span
                    >{Math.round(
                        ((weeksPassed - 18 * WEEKS_PER_YEAR) / ((config.lifeExpectancy - 18) * WEEKS_PER_YEAR)) * 10000,
                    ) / 100}%</span
                >).
            </p>
        </div> -->
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
