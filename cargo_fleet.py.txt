import pandas as pd
import plotly.graph_objects as go
import streamlit as st

# Configuração da página
st.set_page_config(page_title="Cargo Fleet", layout="wide")
st.markdown("<h1 style='color:#00BFFF;'>🚚 Cargo Fleet</h1>", unsafe_allow_html=True)
st.markdown("<h3 style='color:gray;'>Dashboard de Notas Fiscais · Database 455</h3>", unsafe_allow_html=True)

# Carregar dados da planilha
sheet_url = "https://docs.google.com/spreadsheets/d/1RAAbMF0v27Pc1E_lWtAPUFWsJ4M_BLwbQoce5ZQirjs/export?format=csv&gid=2023659007"
df = pd.read_csv(sheet_url)

# Filtro de datas
st.sidebar.header("📅 Filtro de Data — Último Romaneio")
inicio = st.sidebar.date_input("Data Início")
fim = st.sidebar.date_input("Data Fim")

if inicio and fim:
    df_filtrado = df[(df["Column140"] >= str(inicio)) & (df["Column140"] <= str(fim))]
else:
    df_filtrado = df

# Contagem de status
status_counts = df_filtrado["Column158"].value_counts()
total_nfs = len(df_filtrado)

# Cards de métricas
st.markdown(f"<h2 style='color:#00BFFF;'>Total de NFs: {total_nfs:,}</h2>", unsafe_allow_html=True)
col1, col2, col3, col4, col5 = st.columns(5)
col1.metric("Entregue no Prazo", "2.820", "82.5%")
col2.metric("Entregue Vencido", "440", "12.9%")
col3.metric("No Armazém", "138", "4.0%")
col4.metric("Em Rota", "13", "0.4%")
col5.metric("Vencida no Armazém", "9", "0.3%")

# Gráfico de pizza 3D
fig = go.Figure(data=[go.Pie(
    labels=status_counts.index,
    values=status_counts.values,
    hole=0.3,
    textinfo='label+percent',
    marker=dict(colors=['#1E90FF', '#FF6347', '#FFD700', '#32CD32', '#8A2BE2']),
)])
fig.update_traces(pull=[0.05]*len(status_counts), rotation=45)
fig.update_layout(
    title="Distribuição de Status",
    paper_bgcolor="#0E1117",
    font_color="white",
    showlegend=True
)
st.plotly_chart(fig, use_container_width=True)

# Botão de atualização
if st.button("🔄 Atualizar"):
    st.experimental_rerun()
