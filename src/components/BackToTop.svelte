<script>
    import { onMount } from "svelte";
    import { IconArrowUp } from "@tabler/icons-svelte";

    let isVisible = false; // Reactive variable for button visibility

    // Function to handle scroll behavior
    const handleScroll = () => {
        isVisible = window.scrollY > 200;
    };

    // Smooth scroll to top
    const scrollToTop = (event) => {
        event.preventDefault(); // Prevent default anchor behavior
        window.scrollTo({
            top: 0,
            behavior: "smooth"
        });
    };

    onMount(() => {
        // Attach scroll event listener
        window.addEventListener("scroll", handleScroll);

        return () => {
            // Cleanup scroll event listener
            window.removeEventListener("scroll", handleScroll);
        };
    });
</script>

<!-- The Back to Top Button -->
<a
    id="back-to-top"
    class:is-visible={isVisible}
    class:hide={!isVisible}
    on:click={scrollToTop}  
    aria-label="Back to top"
>
    <IconArrowUp id="icon-arrow-up" stroke={2} />
</a>

<style>
/* Back to Top Button */
#back-to-top {
    display: none;
    position: fixed;
    right: 2.5rem;
    bottom: 3rem; /* Position the button at the bottom */
    width: 3rem; /* Set width */
    height: 3rem; /* Set height to match width, ensuring it's a perfect circle */
    padding: 0.6rem 0.7rem;
    background-color: var(--card-background-color);
    color: var(--card-dark-color);
    border-radius: 50%; /* Make it a perfect circle */
    border: solid 1px var(--card-dark-color);
    text-decoration: none;
    text-align: center;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
    transition: all 0.3s cubic-bezier(0.23, 1, 0.32, 1);
    cursor: pointer; /* Change cursor to pointer on hover */
}

#back-to-top:hover {
    box-shadow: 0 6px 12px rgba(255, 255, 255, 0.1);
    transform: translateY(-2px);
    background-color: var(--website-green-color);
}

#back-to-top.is-visible {
    display: block;
    bottom: 3rem; /* Show button when it should be visible */
}

#back-to-top.hide {
    bottom: -3rem; /* Hide the button */
}

</style>
