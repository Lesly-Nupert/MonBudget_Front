<script>
    import { onMount } from 'svelte';
    import { errorPasswordReset, successResetPassword } from "../store";

    let token = '';
    let newPassword = '';

    // Code donné par chatgpt car je n'arrivais pas à récupérer le token

    // Code à exécuter après le montage du composant (composant visible pour l'utilisateur)
    onMount(() => {
     // Récupère le fragment d'URL après le # (hash)
    const hash = window.location.hash;

    // Utilise une expression régulière pour trouver le token après 'resetPassword/'
    const tokenMatch = hash.match(/resetPassword\/(.+)/);

    // Si un token est trouvé, l'assigner à la variable token
    if (tokenMatch) {
      token = tokenMatch[1];
    }
  });

    async function handleResetPassword() {
        try {
            const response = await fetch(
                `${import.meta.env.VITE_API_BASE_URL}resetPassword/${token}`,
                {
                    method: "PATCH",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({ newPassword })
                },
            );

            if (!response.ok) {
                throw new Error(`Erreur HTTP : ${response.status}`);
            }

            const password = await response.json();
            console.log(password);
            console.log("Données soumises avec succès");

            // Retire le message d'erreur
            $errorPasswordReset = "";

            $successResetPassword = "Mot de passe réinitialisé avec succès !";

            setTimeout(() => {
                window.location.href = `#/login`;
                window.location.reload();
            }, 1000);
        } catch (error) {
            $errorPasswordReset = "Erreur veuillez réessayer.";
            console.error("Erreur réseau", error);
        }
    }

</script>



<main class="text-light text-center">
    <section class="formResetPassword text-start pb-5 pt-5">
        <h1 class="text-center mb-5 mt-2 fs-1 text-primary">Réinitialiser votre mot de passe</h1>
        <form on:submit|preventDefault={handleResetPassword}>
            <div class="mb-3">
                <label for="password" class="form-label fs-5">
                    Entrer votre nouveau mot de passe <span aria-hidden="true">*</span>
                </label>
                <input
                    bind:value={newPassword}
                    type="password"
                    class="form-control"
                    id="password"
                    required
                    aria-required
                />
            </div>
            <div class="mb-3">

                <button type="submit" class="btn btn-primary form-control">Réinitialiser</button>

            </div>
            {#if $errorPasswordReset}
                <div class="alert alert-danger" role="alert" aria-live="assertive">
                    {$errorPasswordReset}
                </div>
            {/if}
            {#if $successResetPassword}
                <div class="alert alert-success" role="alert" aria-live="assertive">
                    {$successResetPassword}
                </div>
            {/if}
        </form>
    </section>
</main>




<style>
    main {
        max-width: 600px;
        margin: 0 auto;
        padding: 0 30px;
    }
    h1 {
        font-family: 'Playwrite FR Moderne', sans-serif;
    }

    .btn:hover {
        font-weight: bold;
    }
</style>
