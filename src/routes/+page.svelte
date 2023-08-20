<script lang="ts">
    import { Confetti } from "svelte-confetti";

    // HTML elements
    let stonesToRemoveVal: number;
    let gameResult: string = "";
    let feedText: string = "";

    // Game variables
    let currentPlayer = 1;
    let piles = [3, 5, 7];
    let selectedPile = -1;
    let gameEnded = false;

    const selectPile = async (pileId: number) => {
        selectedPile = pileId;
        stonesToRemoveVal = 1;
    }

    const removeStones = async () => {
        if (gameEnded) return;

        if(stonesToRemoveVal <= piles[selectedPile]) {
            piles[selectedPile] -= stonesToRemoveVal;
            feedText = `Player ${currentPlayer == 1 ? "One" : "Two"} removes ${stonesToRemoveVal} ${stonesToRemoveVal == 1 ? "stone" : "stones"} from pile ${selectedPile + 1}. <br>` + feedText;
            stonesToRemoveVal = 0;
            selectedPile = -1;
            if (await checkWin()) {
                gameEnded = true;
                gameResult = `Player ${(3 - currentPlayer) == 1 ? "One" : "Two"} wins!`;
            } else {
                await switchPlayer();
            }
        } else {
            alert("Invalid number of stones to remove. Choose a valid number.");
        }
    }

    const switchPlayer = async () => {
        currentPlayer = 3 - currentPlayer;
    }

    const checkWin = async () => {
        return piles.every(pile => pile === 0);
    }
</script>

<main>
    <div>
        {#if !gameEnded}
            <h2>Player <span>{currentPlayer}'s</span> turn.</h2>
        {:else}
            <h1>
                <Confetti
                    amount="50"
                    x={[-1, -0.25]}
                    y={[-0.25, 0.25]}
                    delay={[500, 2000]} 
                    xSpread=0.4
                />
    
                {gameResult}
    
                <Confetti
                    amount="50"
                    x={[0.25, 1]}
                    y={[-0.25, 0.25]}
                    delay={[500, 2000]}
                    xSpread=0.4
                />
            </h1>
        {/if}
    </div>
    <div>
        {#each piles as amount, i}
            <div class="pile">
                <strong>Pile {i + 1}:</strong> {amount} stones
                <br>
                <button on:click={async () => await selectPile(i)} style="margin: 10px;">Select pile {i + 1}</button>
            </div>
        {/each}
    </div>
    <div>
        {#if selectedPile > -1}
            <input type="range" placeholder="Enter stones to remove" bind:value={stonesToRemoveVal} max={piles[selectedPile].toString()} min="1">
            <button on:click={async () => removeStones()}>Remove {stonesToRemoveVal} stones</button>
        {/if}
    </div>
    <div>
        {#if !gameEnded}
            <p>{@html feedText}</p>
        {/if}
    </div>
</main>

<style>
	:global(:root) {
		--primary: #ff3e00;
		--primary-light: #ff602b;
		--text-color: #444;
		--text-color-light: #999;
		--text-color-lightest: black;
		--border-color: #edf3f0;
		--bg-well: #f6fafd;
		--bg-body: #fff;
	}

	@media (prefers-color-scheme: dark) {
		:global(:root) {
			--text-color: #b7c0d1;
			--text-color-light: #8e99af;
			--text-color-lightest: white;
			--border-color: #363d49;
			--bg-well: #21242c;
			--bg-body: #16181d;
		}
	}

	:global(body) {
		background: var(--bg-body);
		color: var(--text-color);
		font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
        text-align: center;
		margin: 0 auto;
		padding: 0 1rem 6rem;
	}

	h1 {
		display: flex;
		align-items: center;
        justify-content: center;
		color: var(--text-color-lightest);
	}

	:global(.header svg) {
		width: 100%;
		height: 5px;
	}

    .pile {
        display: inline-block;
        margin: 10px;
        cursor: pointer;
    }
</style>