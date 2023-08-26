-- script que pega todas as procedures/ tabelas utilizadas numa query.
;
WITH stored_procedures AS (
    SELECT o.name AS NomeDaProcedure,
        oo.name AS NomeDaTabela,
        ROW_NUMBER() OVER (
            PARTITION BY o.name,
            oo.name
            ORDER BY o.name,
                oo.name
        ) AS row
    FROM sysdepends d
        INNER JOIN sysobjects o ON o.id = d.id
        INNER JOIN sysobjects oo ON oo.id = d.depid
    WHERE o.xtype = 'P'
)
SELECT NomeDaProcedure,
    NomeDaTabela
FROM stored_procedures
WHERE row = 1
    AND NomeDaProcedure = 'spr_automacao_gerar_pre_fatura' -- PARA FILTRAR PELO NOME DA PROCEDURE
ORDER BY NomeDaProcedure,
    NomeDaTabela
GO 