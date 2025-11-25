<div class="grid-container">
    <div class="center-feed-bar">
    </div>
    <div class="center-feed">
        <h1>cortexa53
            <hr style="margin-top:3px;">
        </h1>
        <div class="center-feed-posts">
            <h2>zipRev</h2>
            <p style="margin-top:0.5%;margin-bottom:2%;font-size:12px;">Haskell; functional programming (10.19.25)</p>
            <p>A question- is it possible to reverse a functional list, then zip it with another list, in one pass?  In Haskell, a solution to this problem looks something like this:</p>
            <pre>zipRev = foldl f b
    where   b = const []
            f z x = \case
                        [] -> []
                        (y:ys) -> (x,y) : z ys
            </pre>
            <p>Note that this requires the <code>LambdaCase</code> extension.</p>
            <p>&nbsp</p>
            <p>The function is a pretty elegant application of <code>foldl</code>, but I don't think its implementation is intuitively obvious - which begs the question of how one might come up with it from scratch.</p>
            <p>&nbsp</p>
            <p>Defining <code>zip</code> in terms of <code>foldr</code> and some folding function <code>g</code> is a potentially fruitful start, 
            since <code>foldl (flip g)</code> can be loosely thought of as folding the first input list in "reverse".</p> 
            <p>&nbsp</p>
            <p><code>zip = foldr g b</code> implies that <code>zip xs = foldr g b xs</code>.</p>
            <p>&nbsp</p>
            <p>Thus, <code>zip [] = b</code> by the definition of <code>foldr</code>, meaning that <code>b = \ys -> []</code> or <code>const []</code> based on the semantics of <code>zip</code>.</p>
            <p>&nbsp</p>
            <p>At the same time, <code>zip (x:xs) = g x (foldr g b xs)</code>, meaning that <code>zip (x:xs) ys = g x (foldr g b xs) ys</code>.  </p>
            <p>&nbsp</p>
            <p>This tells us that our folding function <code>g</code> takes the head of <code>xs</code>, the result of folding its tail, and <code>ys</code> as arguments and returns a zipped list.  Remembering that
            folding its tail yields a function that zips its tail with whatever list you apply it to, we can straightforwardly define <code>g</code> as:</p>
            <pre>g x z = \case
            [] -> []
            (y:ys) -> (x,y) : z ys
            </pre>
            <p>Now, <code>foldl (flip g) b</code> builds up a thunk such that the outermost application of <code>flip g</code> is partially applied to the last element of <code>xs</code>, 
            before being applied to <code>ys</code>.  This is the behavior we want - the elements of <code>xs</code> are being paired up with the elements of <code>ys</code> in reverse order!</p>
            <p>&nbsp</p>
            <p>With this in mind, we can define <code>f = flip g</code> and <code>zipRev = foldl f b</code>.</p>

        </div>
    </div>
</div>

<style>

@import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300..700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Manrope:wght@200..800&display=swap');

.grid-container {
    position: fixed;
    top: 0;
    right: 0;
    height: 100vh;
    width: 100vw;

    overflow: scroll;
    -webkit-overflow-scrolling: touch;

    display: grid;
    grid-template-columns: repeat(9, 1fr);
}

.center-feed {
    height: 100%;
    grid-column-start: 3;
    grid-column-end: 8;
}

.center-feed-bar {
    z-index: 2;
    position: fixed;
    margin: 0;
    padding: 0;
    height: 180px;
    width: 100%;
    overflow: hidden;
    background: white;
}

.center-feed-bar h1 {
    font-family: "Space Grotesk", sans-serif;
    position: relative;
    margin: 0;
    top: 120px;
    left: 200px;
    width: 500px;
}

.center-feed h1{
    font-family: "Space Grotesk", sans-serif;
    z-index: 2;
    position: fixed;
    margin: 0;
    top: 130px;
    
}

.center-feed-posts {
    position: relative;
    z-index: 1;
    top: 200px;
}

.center-feed-posts h2 {
    margin: 0;
    font-family: "Manrope", sans-serif;
}
.center-feed-posts p {
    font-family: "Manrope", sans-serif;
    margin: 0;
}



</style>
