<script>
    let email = '';
    let query = '';

    async function handleSubmit() {
        const data = {
            email,
            contactQuery: query
        };

        try {
            const response = await fetch('http://127.0.0.1:8000/postContactForm/', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(data)
            });

            if (response.ok) {
                alert('Form submitted successfully');
            } else {
                alert('Form submission failed');
            }
        } catch (error) {
            alert('Error: ' + error);
        }
    }
</script>

<main>
    <div class="container mx-auto text-left p-4 md:text-center">
        <div class="relative z-[1] space-y-6 pt-32 pb-24">
            <form on:submit|preventDefault={handleSubmit}>
                <label class="label">
                    <span>Contact form</span>
                    <input bind:value={email} class="input" type="email" placeholder="Email" />
                </label>
                <label class="label">
                    <textarea bind:value={query} class="textarea" rows="4" placeholder="Fill in your query"></textarea>
                </label>
                <button type="submit" class="btn variant-ghost-primary button">Submit</button>
            </form>
        </div>
    </div>
</main>

<style>
    .button {
        margin-right: 11000px;
    }
</style>