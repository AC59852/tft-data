<script>
  let ranks = [
    {
      rank: 'Diamond',
      division: 'IV'
    },
    {
      rank: 'Emerald',
      division: 'IV'
    },
    {
      rank: 'Gold',
      division: 'III'
    },
  ]
</script>

<div class="hero__profile">
  <img src="/" class="hero__icon" alt="">
  <div class="hero__user">
    <div class="hero__ranks">
      {#each ranks as {rank, division}}
        <div class="hero__rank">
          <span class="hero__rank-name">{rank} {division}</span>
        </div>
      {/each}
    </div>
    <h1 class="hero__name">Threekings236 <span class="hero__tagline">#NA1</span></h1>
    <button class="hero__update">Update</button>
  </div>
</div>
<div class="hero__match">
  <h3 class="hero__subtitle">Most Recent Match:</h3>
  <div class="hero__placement">
    <img src="/" alt="" class="hero__tactician">
    <h3 class="hero__subtitle hero__subtitle--placement">4th Place</h3>
    <div class="hero__traits hero__traits--match">
      <img src="/" class="hero__trait hero__trait--match" alt="">
    </div>
  </div>
  <div class="hero__units">
    <img src="/" class="hero__unit hero__unit--match" alt="">
  </div>
</div>