from flask import Flask, jsonify, render_template
import requests

app = Flask(__name__)

USERNAME = "sks"
PASSWORD = "kolbe"

AUTH = (USERNAME, PASSWORD)

BASE = "https://projekttb.sksnr.sk/data"


def get_latest(source):
    url = f"{BASE}/api.php?source={source}&sort=timestamp&dir=desc&limit=1"
    r = requests.get(url, auth=AUTH, timeout=10)
    r.raise_for_status()
    return r.json()["rows"][0]


@app.route("/")
def home():
    return render_template("index.html")


@app.route("/api/dashboard")
def dashboard():

    weather = get_latest(0)
    ambient = get_latest(1)
    door = get_latest(8)

    return jsonify({
        "weather": {
            "temperature": weather["temperature"],
            "feels_like": weather["feels_like"],
            "cloudiness": weather["cloudiness"]
        },
        "ambient": {
            "temperature": ambient["temperature"],
            "co2": ambient["co2"],
            "light": ambient["light_level"]
        },
        "door": {
            "open": door["Exti_pin_level"] == "Low"
        }
    })


@app.route("/camera/<cam>")
def camera(cam):
    r = requests.get(
        f"{BASE}/camera.php?cam={cam}",
        auth=AUTH,
        stream=True,
        timeout=10
    )

    return (
        r.content,
        200,
        {"Content-Type": r.headers.get("Content-Type", "image/jpeg")}
    )


if __name__ == "__main__":
    app.run(debug=True)
