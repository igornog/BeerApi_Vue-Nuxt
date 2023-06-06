<script setup>

const { data } = await useFetch(() => `https://api.punkapi.com/v2/beers?brewed_after=11-2012`);

// Filter the data based on a condition
const filteredBeers = []
data._rawValue.map(item => {
  if (!item.ingredients.hops.some(hop => hop.name === 'Centennial')) {

    // Check if the beer has lactose
    if (item.ingredients.hops.some(hop => hop.name === 'Lactose')) {
      item.hasLactose = true
    } else {
      item.hasLactose = false
    }

    // Check if the beer has dry hop
    if (item.ingredients.hops.some(hop => hop.add === 'dry hop')) {
      item.hasDryHop = true
    } else {
      item.hasDryHop = false
    }
    filteredBeers.push(item)
  }
});

// Sort the data based ABV
filteredBeers.sort((a, b) => {
  if (a.abv > b.abv) {
    return 1;
  }
  if (a.abv < b.abv) {
    return -1;
  }
  return 0;
});


console.log(filteredBeers)
</script>

<template>
  <div>
    <main class='flex flex-col m-16'>
      <h1 class='text-4xl'>Beers</h1>
      <div v-for="beer in filteredBeers " :key="beer.id" class='border mt-4 mb-4 p-8 flex flex-col'>
        <div class='flex'>
          <image class='min-w-[200px]'>
          <img :src="beer.image_url" class="w-auto max-h-[200px] mr-[50px]" />
        </image><div class='flex flex-col gap-[10px]'>
            <h2 class='text-3xl'>{{ beer.name }}</h2>
            <h3 class='text-2xl'>{{ beer.tagline }} {{ beer.hasDryHop && '(dry hop)' }}</h3>
            <p>{{ beer.description }}</p>
            <div class='text-[red]' v-if="beer.hasLactose">
              Warning: contains lactose
            </div>
            <p>ABV: {{ beer.abv }}%</p>
            <p>IBU: {{ beer.ibu }}</p>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>
