
WITH  DOMAINS AS (
        SELECT D.DOM_DOMAIN_AGG1,
            D.DOM_DOMAIN_AGG2,
            D.DOM_DOMAIN_AGG3,
            VERTICAL,
            SIT_SITE_ID SITE_ID,
            SUBSTR(DOM_DOMAIN_ID,5) DOM_DOMAIN_ID
        FROM `meli-bi-data.WHOWNER.LK_DOM_DOMAINS` D
        WHERE VERTICAL IN ('APP & SPORTS','BEAUTY & HEALTH')
       ),
   
   UNIVERSO AS(
        SELECT 
        C.SIT_SITE_ID,
        C.CAT_CATEG_ID_L1,
        C.CAT_CATEG_NAME_L1,
        C.CAT_CATEG_ID_L7,
        C.VERTICAL,
        U.UNIVERSO
        FROM `meli-bi-data.WHOWNER.AG_LK_CAT_CATEGORIES_PH` C
        LEFT JOIN `meli-bi-data.EXPLOTACION.FASHION_FULL_POTENTIAL` U
            ON C.SIT_SITE_ID = U.SIT_SITE_ID AND CAST(C.CAT_CATEG_ID_L1 AS INT64) = U.CAT_CATEG_ID_L1 AND CAST(C.CAT_CATEG_ID_L4 AS INT64) = U.CAT_CATEG_ID_L4
        WHERE C.VERTICAL = 'APPAREL'

    ),
   
    ITEMS AS(
    SELECT DISTINCT 
            ITE.SIT_SITE_ID, 
            ITE.ITE_ITEM_ID,
            ITE_ATT.value_name BRAND_ITEM,
            V.DEVICE,
            SUM(V.QTY_VISITS) VISITS
        FROM `meli-bi-data.WHOWNER.LK_ITE_ITEMS` ITE
        LEFT JOIN UNNEST(ITE_ITEM_ATTRIBUTES) ITE_ATT
        LEFT JOIN DOMAINS D ON ITE.SIT_SITE_ID=D.SITE_ID AND ITE.ITE_ITEM_DOM_DOMAIN_ID = D.DOM_DOMAIN_ID
        LEFT JOIN UNIVERSO U ON ITE.SIT_SITE_ID = U.SIT_SITE_ID AND ITE.CAT_CATEG_ID = CAST(U.CAT_CATEG_ID_L7 AS INT64)
        LEFT JOIN `meli-bi-data.WHOWNER.LK_ITE_ITEM_VISITS_RT` V 
            ON V.SIT_SITE_ID = ITE.SIT_SITE_ID
            AND V.ITE_ITEM_ID=ITE.ITE_ITEM_ID
        WHERE ITE.SIT_SITE_ID IN ('MLA','MLC','MLB','MLM','MLU','MCO','MPE')
        AND (D.DOM_DOMAIN_ID IS NOT NULL OR U.CAT_CATEG_ID_L7 IS NOT NULL)
        and (ITE_ATT.ID = 'BRAND') 
        group by 1,2,3,4
    ),

--    ORDERS AS(
--        SELECT 
--         DATE_TRUNC(DATE(ORD_CLOSED_DTTM),MONTH) DATE_MONTH,
--         DATE(ORD_CLOSED_DTTM) DATE_SERVER,
--         ORD_SELLER.ID SELLER_ID,
--         ORD_BUYER.ID BUYER_ID,
--         ORD_SELLER.OFFICIAL_STORE_ID OFS_STORE_ID,
--         ORD_SELLER.REPUTATION_LEVEL_STATUS,
--         O.SIT_SITE_ID,
--         O.ORD_ORDER_ID, 
--         O.ORD_ITEM.ID ITEM_ID,
--         O.ORD_PACK_ID,
--                 CASE WHEN ORD_SHIPPING.LOGISTIC_TYPE = 'drop_off' then 'DS'
--             WHEN ORD_SHIPPING.LOGISTIC_TYPE = 'xd_drop_off' then 'XD'
--             WHEN ORD_SHIPPING.LOGISTIC_TYPE = 'cross_docking' then 'XD'
--             WHEN ORD_SHIPPING.LOGISTIC_TYPE = 'fulfillment' then 'FBM'
--             WHEN ORD_SHIPPING.LOGISTIC_TYPE = 'self_service' then 'FLEX'
--             ELSE 'Otro' end as LOGISTIC_TYPE_ORDER,
--         ORD_ITEM.SHIPPING.ID SHIPPING_ID,
--         D.DOM_DOMAIN_ID,
--         D.DOM_DOMAIN_AGG1,
--         D.DOM_DOMAIN_AGG2,
--         D.DOM_DOMAIN_AGG3,
--         D.VERTICAL VERTICAL_DOM,
--         U.VERTICAL VERTICAL_CAT,
--         U.CAT_CATEG_NAME_L1,
--         U.UNIVERSO,
--        -- ORD_CATEGORY.ID CAT_CATEG_ID_L7,
--         ORD_SELLER.PARTY_TYPE_ID as PARTY_TYPE,
--         CAST (SUM(ORD_ITEM.QTY * ORD_ITEM.UNIT_PRICE) AS FLOAT64 ) AS GMV_LC,
--         CAST (SUM(ORD_ITEM.QTY * ORD_ITEM.UNIT_PRICE * CC_USD_RATIO )AS FLOAT64) AS GMV,
--         SUM((ORD_ITEM.QTY)) AS SI
--     FROM  `meli-bi-data.WHOWNER.BT_ORD_ORDERS` O
--     LEFT JOIN DOMAINS D ON O.SIT_SITE_ID=D.SITE_ID AND O.DOM_DOMAIN_ID = D.DOM_DOMAIN_ID
--     LEFT JOIN UNIVERSO U ON O.SIT_SITE_ID = U.SIT_SITE_ID AND O.ORD_CATEGORY.ID = CAST(U.CAT_CATEG_ID_L7 AS INT64)
--     WHERE O.SIT_SITE_ID IN ('MLA','MLC','MLB','MLM','MLU','MCO','MPE')
--         AND (D.DOM_DOMAIN_ID IS NOT NULL OR U.CAT_CATEG_ID_L7 IS NOT NULL)
--         AND ORD_TGMV_FLG =TRUE
--         AND TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 3 DAY) < ORD_CLOSED_DTTM
        
--     GROUP BY 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21
--    ),

--    ORDERS_PACKS AS(
--        SELECT
--        O.*,
--        CASE WHEN PACK.PCK_PACK_ID IS NOT NULL THEN 'CARRITO' ELSE 'DIRECTA' END CARRITO_FLAG
--        FROM ORDERS O
--        LEFT JOIN `meli-bi-data.WHOWNER.BT_CRT_PURCHASES_PACKS_ITEMS` PACK -- CRUCE CON LA TABLA DE CARRITOS
--             ON O.ORD_PACK_ID = PACK.PCK_PACK_ID
--             AND O.ITEM_ID = PACK.ITE_ITEM_ID 
--             AND O.SIT_SITE_ID = PACK.SIT_SITE_ID

--    ),


--FILTRAR POR MES UNA VEZ PRODUCTIVO
   TYPE_BUYER AS(
       SELECT 
       SIT_SITE_ID,
       BUYER_ID,
       TIM_MONTH,
       TYPE_BUYER_MONTH
       FROM `meli-bi-data.EXPLOTACION.STAGING_DM_BUYER_TYPE_APPAREL`
       where DATE_ADD( DATE_TRUNC(CURRENT_DATE-1, MONTH),INTERVAL -2 MONTH) < TIM_MONTH 
   ),

   SHIPPING AS(
       SELECT 
       SIT_SITE_ID,
       SHP_SHIPMENT_ID,
       SHP_FREE_FLG
       FROM `meli-bi-data.WHOWNER.BT_SHP_SHIPMENTS`
       WHERE DATE_ADD( DATE_TRUNC(CURRENT_DATE-1, MONTH),INTERVAL -2 MONTH) < SHP_DATE_CREATED_ID --VERIFICAR

   ),
 
    CUSTOMER AS(
        SELECT     
        S.CUS_CUST_ID CUST_ID,
        S.SIT_SITE_ID_CUS SITE_ID,
        DATE_TRUNC(DATE(S.CUS_RU_SINCE_DT),MONTH) CREATION_YEAR,
        SS.SEGMENTO SELLER_SEGMENT
        FROM `meli-bi-data.WHOWNER.LK_CUS_CUSTOMERS_DATA` S
        LEFT JOIN `meli-bi-data.WHOWNER.LK_MKP_SEGMENTO_SELLERS` SS
             ON S.SIT_SITE_ID_CUS=TRIM(SS.SIT_SITE_ID)
             AND S.CUS_CUST_ID=SS.CUS_CUST_ID_SEL
    ),

    TO_BRAND AS(
        SELECT 
        SIT_SITE_ID,
        OFS_OFFICIAL_STORE_ID,
        OFS_BRAND_NAME,
        CASE WHEN UPPER(TRIM(OFS_BRAND_NAME)) IN 
            ('NIKE','ADIDAS','LOREAL','ELC','UNDER ARMOUR','PUMA','PUIG','LACOSTE','PULL & BEAR','LUXOTTICA') 
            THEN 1 ELSE 0 END TOM_BRAND_FLG
        FROM `meli-bi-data.WHOWNER.LK_OFS_BRAND` 
        order by 4 desc
        
    ),


   CLAIMS AS (
    select * from `meli-bi-data.WHOWNER.BT_CM_CLAIMS_V1`,UNNEST(ORD_ORDER_ID) ORDER_ID 
    ),

   DEVOLUCIONES AS(
    select 
    cla.cla_date_claim_opened_dt cla_date_opened,
    CLA.sit_site_id,
    cla.ORDER_ID,
    cla.cla_claim_id,
    (case
      when cla.CLA_STATUS_ID='dispute_opened' then 'Mediación'
      when cla.CLA_STATUS_ID='dispute_closed' then 'Mediación'
      when cla.CLA_STATUS_ID='seller_dispute_opened' then 'Mediación'
      when cla.CLA_STATUS_ID='seller_dispute_closed' then 'Mediación'
      when cla.CLA_STATUS_ID='ml_case_closed' then 'Stale'
      when cla.CLA_STATUS_ID='ml_case_opened' then 'Stale'
      when cla.CLA_STATUS_ID='claim_closed' then 'Reclamo'
      when cla.CLA_STATUS_ID='claim_opened' then 'Reclamo'
      when cla.CLA_STATUS_ID='cancel_purchase' then 'Cancelación Express'
      when cla.CLA_STATUS_ID='return_closed' then 'DevEx'
      when cla.CLA_STATUS_ID='seller_return_closed' then 'DevEx_conflicto'
      when cla.CLA_STATUS_ID='return_opened' then 'DevEx'
      when cla.CLA_STATUS_ID='seller_return_opened' then 'DevEx_conflicto'
      when cla.CLA_STATUS_ID= 'case_opened' then 'Mediación'
      when cla.CLA_STATUS_ID= 'case_closed' then 'Mediación'
      else 'Otros' end)
      as Status,
(case when  cla.cla_reason_detail ='respondent_unanswered' then 'PNR'
when  cla.cla_reason_detail ='estimated_delivery' then 'PNR'
when  cla.cla_reason_detail ='undelivered_other' then 'PNR'
when  cla.cla_reason_detail ='out_of_stock' then 'PNR'
when  cla.cla_reason_detail ='seller_with_no_stock' then 'PNR'
when  cla.cla_reason_detail ='estimated_delivery_out_of_time' then 'PNR'
when  cla.cla_reason_detail ='undelivered_repentant_buyer' then 'PNR'
when  cla.cla_reason_detail ='invalid_shipment_status' then 'PNR'
when  cla.cla_reason_detail ='delivery_date_modified' then 'PNR'
when  cla.cla_reason_detail ='bought_by_mistake' then 'Arrepentimiento'
when  cla.cla_reason_detail ='repentant_buyer' then 'Arrepentimiento'
when  cla.cla_reason_detail ='buyer_repentant' then 'Arrepentimiento'
when  cla.cla_reason_detail ='delivered_out_of_time' then 'Arrepentimiento'
when  cla.cla_reason_detail ='better_price_available' then 'Arrepentimiento'
when  cla.cla_reason_detail ='item_not_useful' then 'Arrepentimiento'
when  cla.cla_reason_detail ='different_color_or_size' then 'Diferente'
when  cla.cla_reason_detail ='different_item_other' then 'Diferente'
when  cla.cla_reason_detail ='different_than_published' then 'Diferente'
when  cla.cla_reason_detail ='item_was_different' then 'Diferente'
when  cla.cla_reason_detail ='empty_box' then 'Defectuoso'
when  cla.cla_reason_detail ='defective_item_other' then 'Defectuoso'
when  cla.cla_reason_detail ='damaged_item' then 'Defectuoso'
when  cla.cla_reason_detail ='not_working_item' then 'Defectuoso'
when  cla.cla_reason_detail ='broken_item' then 'Defectuoso'
when  cla.cla_reason_detail ='damaged_product_other' then 'Defectuoso'
when  cla.cla_reason_detail ='product_stopped_working' then 'Defectuoso'
when  cla.cla_reason_detail ='factory_problem' then 'Defectuoso'
when  cla.cla_reason_detail ='partially_defective' then 'Defectuoso'
when  cla.cla_reason_detail ='missing_accessories' then 'Incompleto'
when  cla.cla_reason_detail ='missing_acessories' then 'Incompleto'
when  cla.cla_reason_detail ='missing_item' then 'Incompleto'
when  cla.cla_reason_detail ='incomplete_item_other' then 'Incompleto'
when  cla.cla_reason_detail ='missing_invoice' then 'Incompleto'
when  cla.cla_reason_detail ='unauthorized_purchase' then 'Compra no autorizada' 
else 'Otros' end) as Tipificacion

FROM CLAIMS CLA
inner join `meli-bi-data.WHOWNER.BT_ORD_ORDERS_CANCELLED` RET ON RET.ORD_ORDER_ID= CLA.ORDER_ID AND ORD_ORDER_CANCELLATION_TYPE ='returns'
    where TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 20 DAY) < CAST(cla.cla_date_claim_opened_dt AS TIMESTAMP)
    )


SELECT 
        O.DATE_SERVER,  
        O.SIT_SITE_ID, 
        O.LOGISTIC_TYPE_ORDER,
        O.OFS_STORE_ID,
        O.PARTY_TYPE,
        O.DOM_DOMAIN_ID,
        O.DOM_DOMAIN_AGG1,
        O.DOM_DOMAIN_AGG2,
        O.DOM_DOMAIN_AGG3,
        O.VERTICAL_DOM,
        O.VERTICAL_CAT,
        O.CAT_CATEG_NAME_L1,
        O.UNIVERSO,
        O.REPUTATION_LEVEL_STATUS,
        O.CARRITO_FLAG,
        C.SELLER_SEGMENT,
        C.CREATION_YEAR,      
        BUY.TYPE_BUYER_MONTH,
        DEV.Tipificacion,
        DEV.Status,
        OFS.OFS_BRAND_NAME,
        OFS.TOM_BRAND_FLG,
        SHP.SHP_FREE_FLG,
        ITE.BRAND_ITEM, 
        ITE.DEVICE,
        SUM(ITE.VISITS),
        COUNT(DISTINCT DEV.cla_claim_id) DEVOLUCIONES,
        SUM(GMV_LC) AS GMV_LC,
    --    SAFE_DIVIDE( SUM(GMV_LC),SUM(ORDERS)) AS ASP_LC,
        SUM(GMV) AS GMV,
    --    SAFE_DIVIDE( SUM(GMV),SUM(ORDERS)) AS ASP,
        SUM(SI) SI,
        COUNT(DISTINCT  ORD_ORDER_ID) AS ORDERS,
        COUNT(DISTINCT O.ITEM_ID) AS NRO_ITEMS,
    --    SAFE_DIVIDE( SUM(SI),SUM(ORDERS)) AS ITEMS_ORDER,
FROM `meli-bi-data.EXPLOTACION.STAGING_DM_ORDERS_APPAREL` O

LEFT JOIN CUSTOMER C 
        ON C.CUST_ID=O.SELLER_ID  
        AND C.SITE_ID = O.SIT_SITE_ID
LEFT JOIN DEVOLUCIONES DEV 
        ON  DEV.sit_site_id = O.SIT_SITE_ID 
        AND DEV.ORDER_ID = O.ORD_ORDER_ID
LEFT JOIN TYPE_BUYER BUY
        ON O.BUYER_ID = BUY.BUYER_ID 
        AND O.DATE_MONTH = buy.TIM_MONTH
LEFT JOIN TO_BRAND OFS 
        ON OFS.SIT_SITE_ID = O.SIT_SITE_ID 
        AND OFS.OFS_OFFICIAL_STORE_ID=O.OFS_STORE_ID 
LEFT JOIN SHIPPING SHP
        ON O.SIT_SITE_ID = SHP.SIT_SITE_ID
        AND O.SHIPPING_ID =SHP.SHP_SHIPMENT_ID
LEFT JOIN ITEMS ITE
        ON ITE.SIT_SITE_ID = O.SIT_SITE_ID
        AND ITE.ITE_ITEM_ID = O.ITEM_ID
GROUP BY 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25
