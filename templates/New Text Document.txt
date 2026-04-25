from flask import Flask, render_template, request
import numpy as np
from tensorflow.keras.models import load_model

app = Flask(__name__)

# Load trained ANN model
model = load_model("Sonar_prediction.h5")

@app.route("/", methods=["GET", "POST"])
def index():
    prediction = None

    if request.method == "POST":
        try:
            # Get 60 input values
            inputs = [float(request.form[f"f{i}"]) for i in range(1, 61)]
            input_array = np.array(inputs).reshape(1, -1)

            # Predict
            result = model.predict(input_array)[0][0]

            prediction = "Mine ⚠️" if result > 0.5 else "Rock 🪨"

        except:
            prediction = "Invalid Input"

    return render_template("index.html", prediction=prediction)

if __name__ == "__main__":
    app.run(debug=True)
