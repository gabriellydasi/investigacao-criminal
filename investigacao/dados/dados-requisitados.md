# 🔗 Dados Requisitados

1. Apresentar maiores compradores de vinhos, considere todos que realizaram mais de 1 compra nos últimos 6 meses. <code>investigacao_compra</code>
```
SELECT 
    COALESCE(p.id, e.id) AS id_comprador,
    COALESCE(p.primeiro_nome || ' ' || p.sobrenome, e.nome) AS nome_comprador,
    COUNT(ic.id) AS qntd_compras,
    CASE 
        WHEN p.id IS NOT NULL THEN 'Pessoa Física'
        ELSE 'Pessoa Jurídica'
    END AS tipo
FROM investigacao_compra ic
    LEFT JOIN pessoa p ON p.id = ic.pessoa_id
    LEFT JOIN empresa e ON e.id = ic.pessoa_id
WHERE ic.data_compra BETWEEN DATE '2024-05-18' AND DATE '2024-11-18'
GROUP BY COALESCE(p.id, e.id), COALESCE(p.primeiro_nome || ' ' || p.sobrenome, e.nome), tipo
HAVING COUNT(ic.id) > 1
ORDER BY qntd_compras DESC;
```

2. Lista de pessoas envolvidas na entrega (todo fluxo, carregamento, transporte e recebimento) <code>investigacao_pessoa_transporte</code>
```
SELECT 
	p.id,
	p.primeiro_nome ||' '|| p.sobrenome AS nome_completo,
	ipt.onde_estava_turno_crime, 
	ipt.local_trabalho, 
	ipt.papel 
FROM investigacao_pessoa_transporte ipt
	JOIN pessoa p ON p.id = ipt.pessoa_id
ORDER BY ipt.papel;
```    

3. Listagem de veículos com características suspeitas <code>veiculo</code>
```
SELECT 
	v.id, 
	v.cor, 
	v.placa, 
	mv.fabricante, 
	mv.tipo
FROM veiculo v
	JOIN modelo_veiculo mv ON mv.id = v.modelo_id
WHERE v.cor = 'azul' 
	AND mv.fabricante = 'Ford'
	AND mv.tipo = 'caminhonete'
ORDER BY v.id;	
```

4. Listagem de pedágio dos veículos com características do suspeito <code>investigacao_pedagio</code>
```
SELECT  
    p.id, 
    p.nome, 
    v.placa, 
    i.data_hora, 
    i.localizacao, 
    i.sentido_trajeto 
FROM investigacao_pedagio i 
	JOIN veiculo v ON v.placa = i.placa
	JOIN proprietario_veiculo pv ON v.id = pv.veiculo_id
	JOIN proprietario pr ON pv.proprietario_id = pr.id
	JOIN proprietario_pessoa_fisica f ON pr.id = f.proprietario_id
	JOIN pessoa p ON p.id = f.pessoa_id 
	JOIN pessoa_caracteristica c ON p.id = c.pessoa_id
WHERE c.cor_cabelo IN ('vermelho', 'laranja')
	AND c.formato_cabelo = 'longo'
	AND c.cor_pele = 'laranja'
	AND c.altura = 'alto'
	AND c.barba = 'Sim'
ORDER BY data_hora DESC;
```

5. Listagem de ligações telefônicas de algumas pessoas investigadas <code>investigacao_ligacao_telefone</code>.
```
SELECT  
    COALESCE(p.id, e.id) AS id_entidade,
    COALESCE(p.primeiro_nome || ' ' || p.sobrenome, e.nome) AS nome,
    CASE 
        WHEN p.id IS NOT NULL THEN 'Pessoa Física'
        ELSE 'Pessoa Jurídica'
    END AS tipo,
    i.origem_telefone_id,
    i.destino_telefone_id,
    i.data_hora
FROM proprietario pr
    LEFT JOIN proprietario_pessoa_fisica f ON f.proprietario_id = pr.id
    LEFT JOIN pessoa p ON p.id = f.pessoa_id
    LEFT JOIN proprietario_pessoa_juridica pj ON pj.proprietario_id = pr.id
    LEFT JOIN empresa e ON e.id = pj.empresa_id
    JOIN proprietario_telefone pt ON pt.proprietario_id = pr.id
    JOIN investigacao_telefone i ON i.origem_telefone_id = pt.telefone_id
WHERE i.data_hora BETWEEN '2024-11-14 00:00:00' AND '2024-11-21 00:00:00'
ORDER BY tipo DESC, nome, data_hora;
```

```
SELECT  
    COALESCE(p.id, e.id) AS id_entidade,
    COALESCE(p.primeiro_nome || ' ' || p.sobrenome, e.nome) AS nome,
    CASE 
        WHEN p.id IS NOT NULL THEN 'Pessoa Física'
        ELSE 'Pessoa Jurídica'
    END AS tipo,
    i.origem_telefone_id,
    i.destino_telefone_id,
    i.data_hora
FROM proprietario pr
    LEFT JOIN proprietario_pessoa_fisica f ON f.proprietario_id = pr.id
    LEFT JOIN pessoa p ON p.id = f.pessoa_id
    LEFT JOIN proprietario_pessoa_juridica pj ON pj.proprietario_id = pr.id
    LEFT JOIN empresa e ON e.id = pj.empresa_id
    JOIN proprietario_telefone pt ON pt.proprietario_id = pr.id
    JOIN investigacao_telefone i ON i.destino_telefone_id = pt.telefone_id 
WHERE i.data_hora BETWEEN '2024-11-14 00:00:00' AND '2024-11-21 00:00:00'
ORDER BY tipo DESC, nome, data_hora;
```