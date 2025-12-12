# Task-4-Improve-Your-Dash-Application-of-forage-certification-of-Quantium-SJS
Description (350 characters): This Dash app visualises Pink Morsels sales with an interactive line chart and region filter for north, south, east, west, or all. Data is sorted by date, and the layout includes custom styling for clarity and appeal. Users can quickly explore regional trends and understand sales patterns

  

code:

import dash
from dash import dcc, html, Input, Output
import plotly.express as px
import pandas as pd

# -----------------------------
# Load / Generate Sample Data
# -----------------------------
# Replace this with the actual DF you generated previously
df = pd.read_csv("pink_morsel_sales.csv")  

df['date'] = pd.to_datetime(df['date'])
df = df.sort_values('date')

# -----------------------------
# Dash App Setup
# -----------------------------
app = dash.Dash(__name__)

app.layout = html.Div(
    style={
        "fontFamily": "Arial, sans-serif",
        "backgroundColor": "#f7f7f7",
        "padding": "20px"
    },
    children=[
        html.H1(
            "Pink Morsels Sales Visualiser",
            style={
                "textAlign": "center",
                "color": "#d63384",
                "marginBottom": "30px",
                "fontWeight": "700",
            },
        ),

        html.Div(
            style={
                "display": "flex",
                "justifyContent": "center",
                "marginBottom": "20px",
                "padding": "15px",
                "backgroundColor": "white",
                "borderRadius": "10px",
                "boxShadow": "0px 2px 8px rgba(0,0,0,0.1)"
            },
            children=[
                dcc.RadioItems(
                    id="region-filter",
                    options=[
                        {"label": "North", "value": "north"},
                        {"label": "East", "value": "east"},
                        {"label": "South", "value": "south"},
                        {"label": "West", "value": "west"},
                        {"label": "All", "value": "all"},
                    ],
                    value="all",
                    inline=True,
                    style={"fontSize": "18px"},
                )
            ]
        ),

        html.Div(
            style={
                "backgroundColor": "white",
                "padding": "20px",
                "borderRadius": "12px",
                "boxShadow": "0px 2px 10px rgba(0,0,0,0.1)"
            },
            children=[
                dcc.Graph(id="sales-line-chart")
            ]
        ),
    ]
)


# -----------------------------
# Callback for Region Filtering
# -----------------------------
@app.callback(
    Output("sales-line-chart", "figure"),
    Input("region-filter", "value")
)
def update_chart(region):
    if region == "all":
        filtered_df = df
    else:
        filtered_df = df[df["region"] == region]

    fig = px.line(
        filtered_df,
        x="date",
        y="sales",
        title=f"Pink Morsels Sales ({region.capitalize() if region!='all' else 'All Regions'})",
    )

    fig.update_layout(
        plot_bgcolor="#fff",
        paper_bgcolor="#fff",
        title_x=0.5,
        xaxis_title="Date",
        yaxis_title="Sales",
        font=dict(size=16)
    )

    return fig


# -----------------------------
# Run the App
# -----------------------------
if __name__ == "__main__":
    app.run_server(debug=True)
