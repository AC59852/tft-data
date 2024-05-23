<script>
  let totalCount = 0;
  const apiKey = 'RGAPI-b2abddd6-df41-4444-ad85-81d51a5230fa';
    const BASE_URL = 'https://na1.api.riotgames.com/tft/league/v1';
    const pageSize = 100; // Assuming each page has 100 items
    let page = 1,
        ranks = ['IRON', 'BRONZE', 'SILVER', 'GOLD', 'PLATINUM', 'DIAMOND', 'MASTER', 'GRANDMASTER', 'CHALLENGER'],
        divisions = ['I', 'II', 'III', 'IV'],
        rank = ranks[0],
        division = divisions[0];

  const getUsersCount = async () => {

    // Check if there's data stored in localStorage
    // const storedData = localStorage.getItem('riotApiData');
    // console.log("Stored data:", storedData)
    // if (storedData) {
    //     const { lastPage, lastTotalCount } = JSON.parse(storedData);
    //     page = lastPage;
    //     totalCount = lastTotalCount;
    // }

    while (true) {
        try {
            const response = await fetch(`${BASE_URL}/entries/${rank}/${division}?queue=RANKED_TFT_DOUBLE_UP&page=${page}&api_key=${apiKey}`);

            if (!response.ok) {
                // Check if the error is a rate limit error
                if (response.status === 429) {
                    console.log("Rate limit exceeded. Saving current progress.");
                    // localStorage.setItem('riotApiData', JSON.stringify({ lastPage: page, lastTotalCount: totalCount }));
                    return totalCount; // Return the count retrieved so far
                } else {
                    throw new Error(`Error: ${response.status} - ${response.statusText}`);
                }
            }

            const usersData = await response.json();

            // If usersData is empty, we have reached the last page
            if (usersData.length === 0) {
                // localStorage.setItem('riotApiData', JSON.stringify({ lastPage: page, lastTotalCount: totalCount }));

                // re-run only this time with the next rank/division. make sure to reset the page to 1
                if (division === divisions[divisions.length - 1]) {
                    if (rank === ranks[ranks.length - 1]) {
                        return totalCount;
                    } else {
                        rank = ranks[ranks.indexOf(rank) + 1];
                        division = divisions[0];
                    }
                } else {
                    division = divisions[divisions.indexOf(division) + 1];
                }

                page = 1;

                continue;
            }

            totalCount += usersData.length;
            page++;

        } catch (error) {
            console.error("Request error:", error);
            // If the error is due to too many requests, save the current progress
            if (error instanceof TypeError && error.message === "Failed to fetch") {
                console.log("Rate limit exceeded. Saving current progress.");
                // localStorage.setItem('riotApiData', JSON.stringify({ lastPage: page - 1, lastTotalCount: totalCount }));
                return totalCount;
            }
            return totalCount; // Return the count retrieved so far even on error
        }
    }
  };

  const fetchData = async () => {
    // const storedData = localStorage.getItem('riotApiData');
    let startRankIndex = 0, startDivisionIndex = 0, startPage = 1;

    // if (storedData) {
    //   const { rank, division, page, totalCount: storedTotalCount } = JSON.parse(storedData);
    //   startRankIndex = ranks.indexOf(rank);
    //   startDivisionIndex = divisions.indexOf(division);
    //   startPage = page;
    //   totalCount = storedTotalCount;
    // }

    totalCount = await getUsersCount();
  };
</script>
<button on:click={fetchData}>Fetch Data</button>
{#if totalCount === 0}
  <p>No data fetched yet.</p>
{:else}
  <h2>Total Count: {totalCount}</h2>
{/if}