import streamlit as st
import pandas as pd
import plotly.express as px
from datetime import datetime
import os

from reportlab.lib.pagesizes import A4
from reportlab.pdfgen import canvas
from reportlab.lib.units import cm

st.set_page_config(
    page_title="EcoTech Solutions",
    page_icon="🌱",
    layout="wide"
)

ARQUIVO_DADOS = "dados_ecotech.csv"

usuarios = {
    "admin": {"senha": "1234", "perfil": "Administrador"},
    "tecnico": {"senha": "1234", "perfil": "Técnico"},
    "usuario": {"senha": "1234", "perfil": "Usuário Comum"}
}

if "logado" not in st.session_state:
    st.session_state.logado = False

if not st.session_state.logado:
    st.title("🌱 EcoTech Solutions")
    st.subheader("Login do Sistema")

    usuario = st.text_input("Usuário")
    senha = st.text_input("Senha", type="password")

    if st.button("Entrar"):
        if usuario in usuarios and senha == usuarios[usuario]["senha"]:
            st.session_state.logado = True
            st.session_state.usuario = usuario
            st.session_state.perfil = usuarios[usuario]["perfil"]
            st.success("Login realizado com sucesso!")
            st.rerun()
        else:
            st.error("Usuário ou senha incorretos.")

    st.info("Teste com: admin / 1234")
    st.stop()


def carregar_dados():
    if os.path.exists(ARQUIVO_DADOS):
        return pd.read_csv(ARQUIVO_DADOS)

    dados_iniciais = pd.DataFrame([
        {
            "Data": "22/04/2026",
            "Setor": "Administração",
            "Consumo (kWh)": 95,
            "Meta (kWh)": 100,
            "Resíduos (L)": 40,
            "Limite Resíduos (L)": 80,
            "Equipamentos Ativos": 8
        },
        {
            "Data": "23/04/2026",
            "Setor": "Produção",
            "Consumo (kWh)": 128,
            "Meta (kWh)": 100,
            "Resíduos (L)": 72,
            "Limite Resíduos (L)": 80,
            "Equipamentos Ativos": 14
        },
        {
            "Data": "24/04/2026",
            "Setor": "TI",
            "Consumo (kWh)": 110,
            "Meta (kWh)": 100,
            "Resíduos (L)": 55,
            "Limite Resíduos (L)": 80,
            "Equipamentos Ativos": 10
        }
    ])

    dados_iniciais.to_csv(ARQUIVO_DADOS, index=False)
    return dados_iniciais


def salvar_dados(df):
    df.to_csv(ARQUIVO_DADOS, index=False)


def card(titulo, valor):
    st.markdown(f"""
    <div class="card">
        <h3>{titulo}</h3>
        <p>{valor}</p>
    </div>
    """, unsafe_allow_html=True)


def gerar_pdf_relatorio(df, nome_arquivo="relatorio_ecotech.pdf"):
    c = canvas.Canvas(nome_arquivo, pagesize=A4)
    largura, altura = A4

    total_consumo = df["Consumo (kWh)"].sum()
    media_consumo = df["Consumo (kWh)"].mean()
    total_residuos = df["Resíduos (L)"].sum()
    media_residuos = df["Resíduos (L)"].mean()

    c.setFont("Helvetica-Bold", 18)
    c.drawString(2 * cm, altura - 2 * cm, "Relatório EcoTech Solutions")

    c.setFont("Helvetica", 11)
    c.drawString(2 * cm, altura - 3 * cm, "Sustentabilidade Inteligente para o Futuro")

    y = altura - 4.5 * cm

    c.setFont("Helvetica-Bold", 13)
    c.drawString(2 * cm, y, "Indicadores Gerais")
    y -= 1 * cm

    c.setFont("Helvetica", 11)
    c.drawString(2 * cm, y, f"Consumo total: {total_consumo:.1f} kWh")
    y -= 0.7 * cm
    c.drawString(2 * cm, y, f"Média de consumo: {media_consumo:.1f} kWh")
    y -= 0.7 * cm
    c.drawString(2 * cm, y, f"Volume total de resíduos: {total_residuos:.1f} L")
    y -= 0.7 * cm
    c.drawString(2 * cm, y, f"Média de resíduos: {media_residuos:.1f} L")
    y -= 0.7 * cm
    c.drawString(2 * cm, y, f"Registros analisados: {len(df)}")

    y -= 1.2 * cm

    c.setFont("Helvetica-Bold", 13)
    c.drawString(2 * cm, y, "Recomendações")
    y -= 0.8 * cm

    recomendacoes = [
        "Monitorar setores com consumo acima da meta.",
        "Programar coletas preventivas quando os resíduos estiverem próximos do limite.",
        "Avaliar equipamentos com uso excessivo.",
        "Criar rotinas automáticas para reduzir desperdícios."
    ]

    c.setFont("Helvetica", 11)
    for rec in recomendacoes:
        c.drawString(2 * cm, y, f"- {rec}")
        y -= 0.7 * cm

    c.save()
    return nome_arquivo


permissoes = {
    "Administrador": [
        "dashboard",
        "registrar",
        "consumo",
        "residuos",
        "relatorio",
        "configuracoes",
        "excluir_dados"
    ],
    "Técnico": [
        "dashboard",
        "registrar",
        "consumo",
        "residuos",
        "relatorio"
    ],
    "Usuário Comum": [
        "dashboard",
        "consumo",
        "residuos"
    ]
}


def tem_permissao(acao):
    perfil = st.session_state.perfil
    return acao in permissoes.get(perfil, [])


def bloquear_acesso(acao):
    if not tem_permissao(acao):
        st.error("🚫 Acesso negado. Seu perfil não tem permissão para esta função.")
        st.stop()


st.markdown("""
<style>
.topo {
    background: linear-gradient(90deg, #1b4332, #2d6a4f);
    padding: 25px;
    border-radius: 16px;
    color: white;
    margin-bottom: 20px;
}
.card {
    background-color: white;
    padding: 18px;
    border-radius: 14px;
    box-shadow: 0px 2px 8px rgba(0,0,0,0.08);
    text-align: center;
}
.card h3 {
    margin: 0;
    font-size: 18px;
    color: #2d6a4f;
}
.card p {
    font-size: 26px;
    font-weight: bold;
    margin: 8px 0 0 0;
    color: #1b4332;
}
.subtitulo {
    font-size: 22px;
    font-weight: bold;
    color: #1b4332;
    margin-top: 20px;
    margin-bottom: 12px;
}
</style>
""", unsafe_allow_html=True)


st.sidebar.title("🌱 EcoTech")
st.sidebar.write(f"Usuário: **{st.session_state.usuario}**")
st.sidebar.write(f"Perfil: **{st.session_state.perfil}**")

opcoes_telas = {
    "🏠 Dashboard": "dashboard",
    "➕ Registrar Dados": "registrar",
    "⚡ Consumo Energético": "consumo",
    "🗑️ Resíduos": "residuos",
    "📊 Relatório Profissional": "relatorio",
    "⚙️ Configurações": "configuracoes"
}

opcoes_menu = [
    tela for tela, permissao in opcoes_telas.items()
    if tem_permissao(permissao)
]

menu = st.sidebar.radio("Navegação", opcoes_menu)

if st.sidebar.button("Sair"):
    st.session_state.logado = False
    st.rerun()


st.markdown(f"""
<div class="topo">
    <h1>🌱 EcoTech Solutions</h1>
    <p>Sustentabilidade Inteligente para o Futuro</p>
    <p><b>Perfil ativo:</b> {st.session_state.perfil}</p>
</div>
""", unsafe_allow_html=True)

df = carregar_dados()


if menu == "🏠 Dashboard":
    bloquear_acesso("dashboard")

    st.markdown('<div class="subtitulo">📌 Visão Geral</div>', unsafe_allow_html=True)

    if df.empty:
        st.warning("Nenhum dado cadastrado ainda. Vá em 'Registrar Dados'.")
    else:
        ultimo = df.iloc[-1]

        consumo = ultimo["Consumo (kWh)"]
        meta = ultimo["Meta (kWh)"]
        residuos = ultimo["Resíduos (L)"]
        limite = ultimo["Limite Resíduos (L)"]

        eficiencia = max(0, min(100, 100 - ((consumo - meta) / meta * 100)))

        if consumo > meta * 1.2:
            st.error("🚨 Consumo crítico! Acima de 120% da meta.")
        elif consumo > meta:
            st.warning("⚠️ Consumo acima da meta.")
        else:
            st.success("✅ Consumo dentro da meta.")

        if residuos >= limite:
            st.error("⚠️ Alerta de coleta: limite de resíduos atingido.")
        elif residuos >= limite * 0.85:
            st.warning("⚠️ Resíduos próximos do limite.")
        else:
            st.success("✅ Resíduos controlados.")

        col1, col2, col3, col4 = st.columns(4)

        with col1:
            card("Consumo Atual", f"{consumo} kWh")
        with col2:
            card("Meta", f"{meta} kWh")
        with col3:
            card("Resíduos", f"{residuos} L")
        with col4:
            if eficiencia >= 90:
                status = "🟢 Excelente"
            elif eficiencia >= 70:
                status = "🟡 Médio"
            else:
                status = "🔴 Crítico"

            card("Eficiência", f"{eficiencia:.1f}%<br>{status}")

        st.markdown('<div class="subtitulo">📈 Histórico Registrado</div>', unsafe_allow_html=True)

        fig = px.line(
            df,
            x="Data",
            y="Consumo (kWh)",
            color="Setor",
            markers=True,
            title="Histórico de Consumo Energético"
        )
        st.plotly_chart(fig, use_container_width=True)

        fig2 = px.bar(
            df,
            x="Data",
            y="Resíduos (L)",
            color="Setor",
            title="Histórico de Resíduos"
        )
        st.plotly_chart(fig2, use_container_width=True)


elif menu == "➕ Registrar Dados":
    bloquear_acesso("registrar")

    st.markdown('<div class="subtitulo">➕ Registrar Dados Operacionais</div>', unsafe_allow_html=True)

    with st.form("form_dados"):
        data = st.date_input("Data", datetime.today())
        setor = st.text_input("Setor ou Unidade", "Unidade Principal")
        consumo = st.number_input("Consumo energético (kWh)", min_value=0.0, value=100.0)
        meta = st.number_input("Meta de consumo (kWh)", min_value=1.0, value=100.0)
        residuos = st.number_input("Volume de resíduos (L)", min_value=0.0, value=50.0)
        limite = st.number_input("Limite de resíduos (L)", min_value=1.0, value=80.0)
        equipamentos = st.number_input("Equipamentos ativos", min_value=0, value=10)

        enviar = st.form_submit_button("Salvar Registro")

        if enviar:
            novo = pd.DataFrame([{
                "Data": data.strftime("%d/%m/%Y"),
                "Setor": setor,
                "Consumo (kWh)": consumo,
                "Meta (kWh)": meta,
                "Resíduos (L)": residuos,
                "Limite Resíduos (L)": limite,
                "Equipamentos Ativos": equipamentos
            }])

            df = pd.concat([df, novo], ignore_index=True)
            salvar_dados(df)
            st.success("Registro salvo com sucesso!")

    st.markdown("### Dados Salvos")
    st.dataframe(df, use_container_width=True)


elif menu == "⚡ Consumo Energético":
    bloquear_acesso("consumo")

    st.markdown('<div class="subtitulo">⚡ Análise de Consumo Energético</div>', unsafe_allow_html=True)

    if df.empty:
        st.warning("Nenhum dado cadastrado.")
    else:
        media_consumo = df["Consumo (kWh)"].mean()
        maior_consumo = df["Consumo (kWh)"].max()
        menor_consumo = df["Consumo (kWh)"].min()

        col1, col2, col3 = st.columns(3)

        with col1:
            card("Média de Consumo", f"{media_consumo:.1f} kWh")
        with col2:
            card("Maior Consumo", f"{maior_consumo:.1f} kWh")
        with col3:
            card("Menor Consumo", f"{menor_consumo:.1f} kWh")

        fig = px.line(
            df,
            x="Data",
            y="Consumo (kWh)",
            color="Setor",
            markers=True,
            title="Consumo por Registro"
        )
        st.plotly_chart(fig, use_container_width=True)

        st.markdown("### 💡 Recomendações")
        st.write("✅ Verificar equipamentos ligados fora do horário de uso.")
        st.write("✅ Avaliar setores com maior consumo.")
        st.write("✅ Automatizar desligamento de equipamentos ociosos.")


elif menu == "🗑️ Resíduos":
    bloquear_acesso("residuos")

    st.markdown('<div class="subtitulo">🗑️ Controle de Resíduos</div>', unsafe_allow_html=True)

    if df.empty:
        st.warning("Nenhum dado cadastrado.")
    else:
        media_residuos = df["Resíduos (L)"].mean()
        maior_residuo = df["Resíduos (L)"].max()

        col1, col2 = st.columns(2)

        with col1:
            card("Média de Resíduos", f"{media_residuos:.1f} L")
        with col2:
            card("Maior Volume", f"{maior_residuo:.1f} L")

        fig = px.bar(
            df,
            x="Data",
            y="Resíduos (L)",
            color="Setor",
            title="Resíduos Registrados"
        )
        st.plotly_chart(fig, use_container_width=True)


elif menu == "📊 Relatório Profissional":
    bloquear_acesso("relatorio")

    st.markdown('<div class="subtitulo">📊 Relatório Profissional EcoTech</div>', unsafe_allow_html=True)

    if df.empty:
        st.warning("Nenhum dado cadastrado para gerar relatório.")
    else:
        media_consumo = df["Consumo (kWh)"].mean()
        media_residuos = df["Resíduos (L)"].mean()
        total_residuos = df["Resíduos (L)"].sum()
        total_consumo = df["Consumo (kWh)"].sum()

        if st.button("📄 Gerar relatório em PDF"):
            arquivo_pdf = gerar_pdf_relatorio(df)

            with open(arquivo_pdf, "rb") as pdf:
                st.download_button(
                    label="⬇️ Baixar relatório em PDF",
                    data=pdf,
                    file_name="relatorio_ecotech.pdf",
                    mime="application/pdf"
                )

        st.write("## Relatório de Sustentabilidade")

        st.write(f"""
        O sistema **EcoTech Solutions** realizou o monitoramento dos dados cadastrados,
        permitindo acompanhar o consumo energético, o volume de resíduos e os indicadores
        de sustentabilidade da organização.

        ### Indicadores Gerais

        - Consumo energético total: **{total_consumo:.1f} kWh**
        - Média de consumo energético: **{media_consumo:.1f} kWh**
        - Volume total de resíduos: **{total_residuos:.1f} L**
        - Média de resíduos gerados: **{media_residuos:.1f} L**
        - Quantidade de registros analisados: **{len(df)}**

        ### Análise

        A solução contribui para a identificação de desperdícios, acompanhamento de metas
        e geração de alertas preventivos. Com os dados registrados, é possível apoiar decisões
        voltadas à redução de custos, eficiência energética e práticas sustentáveis.

        ### Recomendações

        - Monitorar setores com consumo acima da meta.
        - Programar coletas preventivas quando os resíduos estiverem próximos do limite.
        - Avaliar equipamentos com uso excessivo.
        - Criar rotinas automáticas para reduzir desperdícios.
        """)

        st.markdown("### 🤖 Sugestão Inteligente")

        if media_consumo > 110:
            st.write("🔧 Reduzir consumo em setores com maior carga operacional.")
        elif media_residuos > 60:
            st.write("♻️ Implementar coleta mais frequente.")
        else:
            st.write("✅ Operação dentro dos padrões ideais.")

        st.markdown("### Base de Dados do Relatório")
        st.dataframe(df, use_container_width=True)

        csv = df.to_csv(index=False).encode("utf-8")

        st.download_button(
            label="⬇️ Baixar relatório em CSV",
            data=csv,
            file_name="relatorio_profissional_ecotech.csv",
            mime="text/csv"
        )


elif menu == "⚙️ Configurações":
    bloquear_acesso("configuracoes")

    st.markdown('<div class="subtitulo">⚙️ Configurações</div>', unsafe_allow_html=True)

    st.write("### Usuários disponíveis para teste")
    st.write("- admin / 1234")
    st.write("- tecnico / 1234")
    st.write("- usuario / 1234")

    if tem_permissao("excluir_dados"):
        st.markdown("### 🧹 Administração dos Dados")

        if st.button("Apagar todos os dados salvos"):
            if os.path.exists(ARQUIVO_DADOS):
                os.remove(ARQUIVO_DADOS)
                st.success("Dados apagados com sucesso.")
                st.rerun()
            else:
                st.info("Nenhum arquivo de dados encontrado.")

    st.warning("Nesta versão, os usuários são fixos no código. Em uma versão futura, podemos colocar banco de dados.")
