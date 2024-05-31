<script>
  import { PUBLIC_BASE_URL } from '$env/static/public';
 	import axios from "axios";


	 let ergebnis = false;
    let model = "";
    let kilometers = 0;
    let year = "";
    let predicted_price;
    let desired_price = 0;
    let satisfaction = "";

    const model_encoded = [
        { id: 0, name: "A Class" },
        { id: 1, name: "C Class" },
        { id: 2, name: "S Class" },
        { id: 3, name: "GLC Class" },
        { id: 4, name: "GLA Class" },
        { id: 5, name: "CLA Class" },
        { id: 6, name: "CLS Class" },
        { id: 7, name: "G Class" },
        { id: 8, name: "E Class" },
    ];

    function predictPrice() {
        if (year === "") {
            alert("Bitte wählen Sie ein Baujahr aus.");
            return;
        }

        if (model === "") {
            alert("Bitte wählen Sie ein Modell aus.");
            return;
        }

        let url =
            base_url +
            "/api/prediction/car?model=" +
            model.id +
            "&kilometers=" +
            kilometers +
            "&year=" +
            year;

        axios.get(url).then((response) => {
            predicted_price = response.data;
            ergebnis = true;
            calculateSatisfaction();
        });
    }

    function calculateSatisfaction() {
        if (predicted_price === desired_price) {
            satisfaction = "Zufrieden";
        } else if (predicted_price < desired_price) {
            satisfaction = "Zufrieden (🙂)";
        } else {
            satisfaction = "Unzufrieden (☹️)";
        }
    }

    function validateKilometers() {
        if (
            isNaN(kilometers) ||
            kilometers < 0 ||
            /^0[0-9].*/.test(kilometers)
        ) {
            alert("Bitte geben Sie eine gültige Kilometerzahl ein.");
            kilometers = 0;
        }
    }
</script>

<div class="container text-center">
    <h1>Auto Preis Vorhersage</h1>
    <br />
    <p>
        Bitte geben Sie das Modell, die Kilometer und das Baujahr des Autos ein.
    </p>
    <div class="row container text-center">
        <div class="d-flex justify-content-center">
            <table class="table table-borderless" style="width: 500px;">
                <thead>
                    <tr>
                        <th scope="col">Modell</th>
                        <th scope="col">Kilometer</th>
                        <th scope="col">Baujahr</th>
                        <th scope="col">Gewünschter Preis</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>
                            <select bind:value={model}>
                                <option value="">Bitte wählen</option>
                                {#each model_encoded as model}
                                    <option value={model}>
                                        {model.name}
                                    </option>
                                {/each}
                            </select>
                        </td>
                        <td>
                            <input
                                type="text"
                                class="form-control"
                                aria-label="kilometers"
                                bind:value={kilometers}
                                on:input={validateKilometers}
                            />
                        </td>
                        <td>
                            <select bind:value={year}>
                                <option value="">Bitte wählen</option>
                                {#each Array.from({ length: 24 }, (_, i) => 2000 + i) as y}
                                    <option value={y}>{y}</option>
                                {/each}
                            </select>
                        </td>
                        <td>
                            <input
                                type="number"
                                class="form-control"
                                min="0"
                                step="1"
                                aria-label="desired-price"
                                bind:value={desired_price}
                            />
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
    <div class="row container text-center">
        <div class="d-flex justify-content-center">
            <button
                type="button"
                class="btn btn-primary"
                on:click={predictPrice}>Preis vorhersagen</button
            >
        </div>
    </div>
    {#if ergebnis}
        <p>Der vorhergesagte Preis ist: CHF {predicted_price}</p>
        <p>Zufriedenheit: {satisfaction}</p>
    {/if}
  </div>
    

<style>
    .container {
        max-width: 960px;
        margin: auto;
        padding: 1em;
    }

    h1 {
        color: #444;
        text-align: center;
        margin-bottom: 1em;
    }

    table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 2em;
        box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.1);
    }

    table th {
        background-color: #555;
        color: #fff;
        padding: 10px;
        text-align: left;
    }

    table td {
        background-color: #fff;
        padding: 10px;
    }

    button {
        margin-top: 1em;
        padding: 10px 20px;
        color: #fff;
        border: none;
        border-radius: 5px;
        box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
        cursor: pointer;
    }

    button:hover {
        background-color: #2980b9;
    }
</style>

