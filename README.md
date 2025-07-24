# Temperature-Converter
#HTML Code

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Temperature Converter</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 30px;
            background-color: #f4f4f4ff;
        }
        form {
            background: #fff;
            padding: 20px;
            border-radius: 10px;
            width: 300px;
            margin: auto;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.77);
        }
        input, select, button {
            margin: 10px 0;
            width: 100%;
            padding: 8px;
            color : Black;
        }
    </style>
</head>
<body>
    <h2 style="text-align:center;">Temperature Converter</h2>
    <form method="post">
        <label>Temperature:</label>
        <input type="number" step="any" name="value" required>

        <label>From:</label>
        <select name="from_unit">
            <option value="Celsius">Celsius</option>
            <option value="Fahrenheit">Fahrenheit</option>
            <option value="Kelvin">Kelvin</option>
        </select>

        <label>To:</label>
        <select name="to_unit">
            <option value="Celsius">Celsius</option>
            <option value="Fahrenheit">Fahrenheit</option>
            <option value="Kelvin">Kelvin</option>
        </select>

        <button type="submit">Convert</button>

        {% if result is not none %}
            <p><strong>Result:</strong> {{ result | round(2) }}</p>
        {% elif error %}
            <p style="color:red;"><strong>Error:</strong> {{ error }}</p>
        {% endif %}
    </form>
</body>
</html>

#Python Code
from flask import Flask, render_template, request

app = Flask(__name__)

def convert_temperature(value, from_unit, to_unit):
    from_unit = from_unit.lower()
    to_unit = to_unit.lower()

    if from_unit == to_unit:
        return value

    if from_unit == 'celsius':
        if to_unit == 'fahrenheit':
            return (value * 9/5) + 32
        elif to_unit == 'kelvin':
            return value + 273.15
    elif from_unit == 'fahrenheit':
        if to_unit == 'celsius':
            return (value - 32) * 5/9
        elif to_unit == 'kelvin':
            return (value - 32) * 5/9 + 273.15
    elif from_unit == 'kelvin':
        if to_unit == 'celsius':
            return value - 273.15
        elif to_unit == 'fahrenheit':
            return (value - 273.15) * 9/5 + 32

    raise ValueError("Invalid temperature units.")

@app.route('/', methods=['GET', 'POST'])
def index():
    result = None
    error = None

    if request.method == 'POST':
        try:
            value = float(request.form['value'])
            from_unit = request.form['from_unit']
            to_unit = request.form['to_unit']
            result = convert_temperature(value, from_unit, to_unit)
        except ValueError as e:
            error = str(e)

    return render_template('index.html', result=result, error=error)

if __name__ == '__main__':
    app.run(debug=True)

#Server Code
from waitress import serve
from app import app

serve(app, host='0.0.0.0', port=8000)

