<template>
    <div class="person">
        <div class="person__card">
            <div class="person__photo">
                <img :src="person.picture" alt="photo" />
            </div>
            <div class="person__info">
                <div class="person__info-header">
                    <div class="person__info-name">
                        {{ person.name }}
                    </div>
                    <div class="person__info-age">
                        Возраст: {{ person.age }}
                    </div>
                    <div class="person__info-reg">
                        Дата регистрации: {{ formatedDate }}
                    </div>
                </div>
                <div class="person__info-footer">
                    <div v-tooltip="person.email" class="person__info-email">
                        <a :href="`mailto:${person.email}`">
                            <span class="icon">
                                <IconMail />
                            </span>
                        </a>
                    </div>

                    <div
                        @click="handleBioShow"
                        v-tooltip="bioTooltip"
                        class="person__info-bio"
                    >
                        <span class="icon">
                            <IconBio />
                        </span>
                    </div>
                </div>
            </div>
        </div>
        <div v-if="isBioOpened" class="person__about">
            <p class="person__about-title">О сотруднике</p>
            <div>{{ person.about }}</div>
        </div>
    </div>
</template>

<script>
import { format } from 'date-fns';
import IconMail from '@/components/Icons/IconMail.vue';
import IconBio from '@/components/Icons/IconBio.vue';
import { VTooltip } from 'v-tooltip';
import { BIO_TOOLTIP_SHOW, BIO_TOOLTIP_HIDE } from './consts';
export default {
    name: 'PersonCard',
    components: {
        IconMail,
        IconBio,
    },
    directives: {
        tooltip: VTooltip,
    },
    props: {
        person: {
            type: Object,
            default: null,
        },
    },
    data() {
        return {
            bioTooltip: BIO_TOOLTIP_SHOW,
            isBioOpened: false,
        };
    },
    methods: {
        handleBioShow() {
            this.isBioOpened = !this.isBioOpened;
            if (this.isBioOpened) {
                this.bioTooltip = BIO_TOOLTIP_SHOW;
            } else {
                this.bioTooltip = BIO_TOOLTIP_HIDE;
            }
        },
    },
    computed: {
        formatedDate() {
            return format(new Date(this.person.registered), 'dd.MM.yyyy');
        },
    },
};
</script>

<style scoped>
.person__card {
    display: grid;
    grid-template-columns: 175px 1fr;
    gap: 10px;
}

.person__photo {
    border-radius: 10px;
    overflow: hidden;
    width: 175px;
    height: 175px;
}

.person__photo img {
    max-width: 100%;
    height: auto;
}

.person__info {
    display: flex;
    flex-direction: column;
}

.person__info-footer {
    margin-top: auto;
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.person__info-name {
    color: #005bff;
    font-weight: 700;
    font-size: 18px;
    margin-bottom: 10px;
}
.person__info-age,
.person__info-reg {
    color: #8d8d8d;
    margin-bottom: 3px;
}

.person__info-bio {
    cursor: pointer;
}

.person__about {
    margin-top: 30px;
}

.person__about-title {
    color: #005bff;
    font-size: 18px;
    font-weight: 700;
}

.icon {
    width: 32px;
    height: 32px;
    display: grid;
    place-items: center;
    background-color: #b8d9ff;
    border-radius: 8px;
}
.icon svg {
    width: 20px;
    height: 20px;
    fill: #0059ff;
}
.icon .icon-mail {
    position: relative;
    top: -1px;
}
</style>

<style>
.tooltip {
    pointer-events: none;
    padding: 4px;
    z-index: 10000;
}

.tooltip .tooltip-inner {
    background: #0059ff;
    color: white;
    border-radius: 8px;
    padding: 5px 10px 4px;
    max-width: 200px;
    text-align: center;
}
</style>
