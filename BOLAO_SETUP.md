# ⚽ Bolão Brasileirão 2026 — Guia de Setup

Site novo, separado do app da Copa, focado no Campeonato Brasileiro Série A
2026 (que já está rolando!). Mesmo modelo: 1 grupo só (seus amigos/família),
de graça, hospedado no GitHub Pages.

---

## PASSO 1: Criar um Supabase NOVO (separado do da Copa)

Como é um app diferente, recomendo um projeto Supabase **separado** do da
Copa, para não misturar usuários/dados.

1. Acesse **supabase.com** → New project
2. Nome: `bolao-brasileirao`
3. Região: South America (São Paulo)
4. Aguarde a criação (~2 min)

## PASSO 2: Criar as tabelas

No SQL Editor do Supabase, rode:

```sql
CREATE TABLE IF NOT EXISTS usuarios (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  nome TEXT NOT NULL,
  usuario TEXT UNIQUE NOT NULL,
  senha_hash TEXT NOT NULL,
  foto_url TEXT,
  time_favorito TEXT,
  criado_em TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE IF NOT EXISTS palpites (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  usuario_id UUID REFERENCES usuarios(id) ON DELETE CASCADE,
  jogo_id TEXT NOT NULL,
  gols1 INT NOT NULL DEFAULT 0,
  gols2 INT NOT NULL DEFAULT 0,
  criado_em TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(usuario_id, jogo_id)
);

ALTER TABLE usuarios ENABLE ROW LEVEL SECURITY;
ALTER TABLE palpites ENABLE ROW LEVEL SECURITY;
CREATE POLICY "usuarios_total" ON usuarios FOR ALL USING (true) WITH CHECK (true);
CREATE POLICY "palpites_total" ON palpites FOR ALL USING (true) WITH CHECK (true);
```

## PASSO 3: Pegar as credenciais

Project Settings (⚙️) → Data API → copie:
- **Project URL**
- **anon public** key (formato `sb_publishable_...` ou `eyJ...`)

## PASSO 4: Configurar o index.html

Abra `index.html`, procure por:

```javascript
const CFG = {
  supabase_url: 'SUPABASE_URL_AQUI',
  supabase_key: 'SUPABASE_ANON_KEY_AQUI',
  ...
```

Substitua pelos valores reais do seu projeto.

**Importante:** o admin está configurado como `['paulo']` — se quiser outro
usuário como admin, me avisa.

## PASSO 5: Subir no GitHub Pages

1. Crie um **novo repositório** no GitHub (ex: `bolao-brasileirao`)
2. Suba os 3 arquivos: `index.html`, `icon.svg`, `_headers`
3. Settings → Pages → Source: Deploy from a branch → main → / (root)
4. Aguarde 1-2 min e o link aparece: `https://seu-usuario.github.io/bolao-brasileirao/`

---

## O que o app já tem

- ⚽ **Login simples** (usuário + senha) — mesmo padrão do Copa
- 📅 **Jogos de hoje** + lista completa filtrada por Próximos/Ao Vivo/Encerrados
- 🔴 **Placar ao vivo** via ESPN (slug `bra.1`, mesmo padrão da Copa)
- 📊 **Tabela de classificação** em tempo real, com zona de Libertadores
  (verde) e rebaixamento (vermelho) destacadas
- 🎯 **Palpites** com placar exato (40pts) / resultado certo (15pts)
- 💛 **Time do coração** — escolhe 1 dos 20 times e ganha +5 pts bônus
  sempre que ele jogar
- 🏅 **Ranking** com pódio e lista completa
- 👀 **Ver palpites de todos** em cada jogo
- ⚙️ **Painel Admin** — editar placar manualmente se a API falhar
- 📲 **Instalável como PWA** na tela inicial
- ☀️🌙 **Tema claro/escuro**
- 🔄 **Auto-atualização** — sempre que eu mandar uma versão nova, todo
  mundo recebe sozinho sem precisar fazer nada

## O que NÃO tem (deixei de fora para ficar enxuto, como você pediu)

- Chat da família
- Stories pós-jogo
- Auditoria avançada (dispositivos, horário de pico, etc — só o básico de admin)
- Música de fundo
- Notificações push
- Molduras de avatar por nível
- Bolão de campeão / classificação de grupos (o Brasileirão não tem grupos)

Se mais pra frente quiser adicionar algo da lista acima, é só pedir —
o código já está estruturado de forma parecida com o da Copa, então é
rápido de portar qualquer feature de lá pra cá.

## ✅ Checklist

- [ ] Supabase novo criado com as 2 tabelas
- [ ] Credenciais coladas no `index.html`
- [ ] Repositório GitHub criado com os 3 arquivos
- [ ] GitHub Pages ativado
- [ ] Testar cadastro de usuário
- [ ] Testar dar um palpite em jogo futuro
- [ ] Conferir se aparece jogo ao vivo (se houver algum rolando)
