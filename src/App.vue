<script setup>
    import { onMounted, reactive, ref } from 'vue';
    import axios from 'axios';
    import md5 from 'md5';
    import SuggestList from '@/components/SuggestList.vue';
    import Form from '@/components/Form.vue';
    import Comics from '@/components/Comics.vue'

    const characters = ref(
        { 
            name: '', 
            description: '', 
            events : { items: '' },
            comics: { items: '' },
            series: { items: '' },
            stories: { items: '' },
            thumbnail: { path: '', extension: '' } 
        }
    );
    characters.value = reactive(characters.value);
    const publicKey = 'bcd878846679170e51cd00c78f4c6a17';
    const privateKey = '52c0cc34866afbce5dd9d20b74ad049e198c8d1b';
    const ts = new Date().getTime();
    const hash = md5(ts + privateKey + publicKey);
    const name = ref('');
    const suggestsOption = ref('')
    const selectedHero = ref(null);
    const isLoaded = ref('false');
    const listOfSuggests = ref([]);
    const imdSuggest = ref([]);
    const isEvents = ref(false);
    const isComics = ref(false);
    const isSeries = ref(false);
    const isStories = ref(false);
    const showEventsTitle = ref('Show events');
    const showComicsTitle = ref('Show comics');
    const showSeriesTitle = ref('Show series');
    const showStoriesTitle = ref('Show stories');


    const getCharacher = async () => {
        const res = await axios(`https://gateway.marvel.com/v1/public/characters?nameStartsWith=${name.value}&ts=${ts}&apikey=${publicKey}&hash=${hash}`);
        const { data: { data: { results } } } = res;
        console.log(results);

        if (results.length < 1 || name.value === '') {
            isLoaded.value = false;
            return
        } else {
            suggestsOption.value = 'Suggested options:'
            isLoaded.value = true
        }

        characters.value = results.map(character => ({
            name: character.name,
            description: character.description,
            events: {
                items: character.events.items
            },
            comics: {
                items: character.comics.items
            },
            series: {
                items: character.series.items
            },
            stories: { 
                items: character.stories.items 
            },
            thumbnail: {
                path: character.thumbnail.path,
                extension: character.thumbnail.extension
            }
        }));

        // console.log(characters.value[0].events.items[0].name);
        // eventList.value = [];
        // characters.value.forEach(character => {
        //     eventList.value.push(character.events.items.forEach(e => {
        //         console.log(e.name);
        //     }))
        // })
        // console.log(eventList.value);


        listOfSuggests.value = [];
        results.forEach(character => {
            listOfSuggests.value.push(character.name)
            });

        imdSuggest.value = [];
        results.forEach(character => {
            imdSuggest.value.push(character.thumbnail.path + '.' + character.thumbnail.extension)
        })
        // console.log(listOfSuggests.value);
        // console.log(imdSuggest.value);
        // console.log(results[selectedHero.value]);
        // console.log(listOfSuggests.value[1]);
    }

    const showHeroInfo = (index) => {
        selectedHero.value = index;
    }

    // const showDesc = (index) => {
    //         heroDesc.value = results[index].description
    //     }

    const backBtn = () => {
        selectedHero.value = null;
        isComics.value = false;
        isEvents.value = false;
        isSeries.value = false;
        isStories.value = false;
    }

    const showEvents = () => {
        isEvents.value = !isEvents.value;
        showEventsTitle.value = isEvents.value ? 'Hide events' : 'Show events';
    }

    const showComics = () => {
        isComics.value = !isComics.value;
        showComicsTitle.value = isComics.value ? 'Hide comics' : 'Show comics';
    }

    const showSeries = () => {
        isSeries.value = !isSeries.value;
        showSeriesTitle.value = isSeries.value ? 'Hide series' : 'Show series'
    }
    const showStories = () => {
        isStories.value = !isStories.value;
        showStoriesTitle.value = isStories.value ? 'Hide stories' : 'Show stories'

    }

    onMounted(getCharacher)
</script>

<template>
    <div class="block">
        <h1>Marvel API</h1>
        <div class="block__inner">
            <Form 
            v-model="name"
            @input-emit="getCharacher"
            />
            <!-- <form>
                <input type="text" v-model="name" placeholder="Enter the character name" @input="getCharacher">
            </form> -->
        <div class="res" v-if="isLoaded">
            <p>{{ suggestsOption }}</p>
            <div class="all-lists">
                <SuggestList 
                :suggests="listOfSuggests"
                :images="imdSuggest"
                :selected="selectedHero"
                @show-hero="showHeroInfo"
                />
                <!-- <div class="suggests__list" v-for="(character, index) in listOfSuggests" :key="index" v-if="selectedHero === null">
                    <p>{{ index + 1 }}. {{ character }}</p>
                    <img class="suggest-img" :src="imdSuggest[index]" alt="thumnail" @click="showHeroInfo(index)">
                </div> -->
                <div v-if="selectedHero !== null">
                    <h2>{{ listOfSuggests[selectedHero] }}</h2>
                    <img class="selected-hero-img" :src="imdSuggest[selectedHero]" alt="hero-thumbnail"><br>
                    <div class="desc-block" v-if="characters[selectedHero].description.length > 0">
                        <h1>Description</h1>
                        <p class="desc">{{ characters[selectedHero].description }}</p>
                    </div>
                    <div v-else class="desc-block">
                        <p>No have a desc</p>
                    </div>
                    <div class="comics-events-series-stories">
                        <Comics 
                        @show-comics="showComics"
                        :iscomics="isComics"
                        :comicstitle="showComicsTitle"
                        :selected="selectedHero"
                        :chrctrs="characters"
                        />
                            <!-- <div class="comics">
                                <button @click="showComics" class="show-btn">{{ showComicsTitle }}</button>
                                <div v-if="isComics" class="items">
                                    <div v-if="characters[selectedHero].comics.items.length > 0">
                                        <div v-for="comics in characters[selectedHero].comics.items">
                                            <p>{{ comics.name }}</p>
                                        </div>
                                    </div>
                                <div v-else>
                                    <p>No comics</p>
                                </div>
                            </div>
                        </div> -->
                        
                        <div class="events">
                            <button @click="showEvents" class="show-btn">{{ showEventsTitle }}</button>
                            <div v-if="isEvents" class="items">
                                <div v-if="characters[selectedHero].events.items.length > 0">
                                    <div v-for="event in characters[selectedHero].events.items">
                                        <p> {{ event.name }} </p>
                                    </div>
                                </div>
                                <div v-else>
                                    <p>No events</p>
                                </div>
                            </div>
                        </div>
                        
                        <div class="series">
                            <button @click="showSeries" class="show-btn">{{ showSeriesTitle }}</button>
                            <div v-if="isSeries" class="items">
                                <div v-if="characters[selectedHero].series.items.length > 0">
                                    <div v-for="episode in characters[selectedHero].series.items">
                                        <p>{{ episode.name }}</p>
                                    </div>
                                </div>
                                <div v-else>
                                    <p>No series</p>
                                </div>
                            </div>
                        </div>
                        
                        <div class="stories">
                            <button @click="showStories" class="show-btn">{{ showStoriesTitle }}</button>
                            <div v-if="isStories" class="items">
                                <div v-if="characters[selectedHero].stories.items.length > 0">
                                    <div v-for="story in characters[selectedHero].stories.items">
                                        <p>{{ story.name }}</p>
                                    </div>
                                </div>
                                <div v-else>
                                    <p>No stories</p>
                                </div>
                            </div>
                        </div>
                    </div>
                    <button @click="backBtn" style="margin-top: 50px;">Back</button>
                </div>
            </div>
        </div>
        <div class="res" v-else>
            <p>No such a character</p>
        </div>
        </div>
    </div>
</template>

