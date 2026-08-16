# Segurança da agenda — checklist do Rodrigo (~5 min no painel do Supabase)

A plataforma agora tem tela de login com 3 contas fixas (PH, Fabiano, Rodrigo).
As contas `ph@ignicao.ph` e `fabiano@ignicao.ph` já foram criadas via API, mas estão
**pendentes de confirmação** (o projeto está com confirmação de e-mail ligada e esses
endereços são internos, não recebem e-mail). Falta você destravar no painel:

## 1. Confirmar as duas contas criadas
Authentication → Users → `ph@ignicao.ph` → menu **⋯** → **Confirm email**.
Repita para `fabiano@ignicao.ph`.

## 2. Criar a sua conta
Authentication → Users → **Add user** → e-mail `rodrigo@ignicao.ph` + a senha combinada
no grupo → marque **Auto Confirm User**.
(As senhas de todos foram definidas pelo Philipe e passadas no grupo — não estão neste repositório.)

## 3. Desligar cadastro de novos usuários
Authentication → Sign In / Providers (Email) → **desmarcar "Allow new users to sign up"** → Save.
Sem isso, qualquer pessoa com a chave pública consegue criar conta e entrar.

## 4. Trancar a tabela da agenda (SQL Editor → colar → Run)

```sql
alter table public.agenda enable row level security;

-- apague antes, em Database → Policies, qualquer policy antiga que libere "anon"

create policy "time_le"       on public.agenda for select to authenticated using (true);
create policy "time_insere"   on public.agenda for insert to authenticated with check (true);
create policy "time_atualiza" on public.agenda for update to authenticated
  using (true) with check (true);
```

Depois disso, **sem login nada lê nem escreve** — a chave `anon` que fica no código
passa a servir só para autenticar; sozinha, não dá mais acesso à agenda.

## 5. Testar
Abra a plataforma → entre como Rodrigo → salve um dia no calendário → abra em outro
aparelho como PH e confirme que aparece. Senha errada tem que ser recusada.

> Esquema esperado da tabela: `agenda(id int primary key, data jsonb)` — linha única `id=1`.
