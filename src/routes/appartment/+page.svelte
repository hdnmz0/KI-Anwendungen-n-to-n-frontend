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
            alert("Please choose a year.");
            return;
        }

        if (model === "") {
            alert("Please choose a model.");
            return;
        }

        let url =
           PUBLIC_BASE_URL +
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
            satisfaction = "Satisfied";
        } else if (predicted_price < desired_price) {
            satisfaction = "Satisfied!";
        } else {
            satisfaction = "Unsatisfied :(";
        }
    }

    function validateKilometers() {
        if (
            isNaN(kilometers) ||
            kilometers < 0 ||
            /^0[0-9].*/.test(kilometers)
        ) {
            alert("Please choose a valid kilometers.");
            kilometers = 0;
        }
    }
</script>

<div class="container text-center">
    <h1>Price Prediction</h1>
    <br />
    <div class="text-content">
        to predict, enter the model, mileage and year of the car! 😎👍
      </div>
    <div class="row container text-center">
        <div class="d-flex justify-content-center">
            <table class="table table-borderless" style="width: 700px;">
                <thead>
                    <tr>
                        <th scope="col">Model</th>
                        <th scope="col">Mileage</th>
                        <th scope="col">Year</th>
                        <th scope="col">Desired Price</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>
                            <select bind:value={model}>
                                <option value="">Choose</option>
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
                                <option value="">Choose</option>
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
                on:click={predictPrice}>Predict!</button
            >
        </div>
    </div>
    {#if ergebnis}
         <div class="text-content">
            The predicted price: CHF {predicted_price}
          </div>
          <div class="text-content">
            Satisfied? {satisfaction}
          </div>
    {/if}
  </div>
    

<style>
    .container {
        max-width: 960px;
        margin: auto;
        padding: 1em;
	
    }

    h1 {
       font-family: "Helvetica", sans-serif;
        font-size: 4em;
        font-weight: bold;
        color: #0b3847;
        text-align: center;
    }

    .text-content {
        font-size: 2em;
  	color: #555;
  	text-align: center;
    }


    table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 2em;
        box-shadow: #0b3847;
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
        background-color: #1d5a80;
    }
</style>
