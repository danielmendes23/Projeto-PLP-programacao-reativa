# Arquitetura da implementação de referência

```text
src/infrastructure/sensor-simulator.ts  ---> Observable<TemperatureReading>
                                               |
                                               v
src/application/monitor.ts          scan(advanceMonitor) -> filter(eventos) -> Observable<MonitorEvent>
                                               ^
                                               |
src/domain/temperature-policy.ts (regras puras + estado finito, sem bibliotecas)
                                               |
                                               v
src/presentation/console-reporter.ts  <-- src/main.ts (composition root)
```

## Máquina de estados

Estado: `{consecutiveHighs: 0|1|2, alerted: boolean}`. Ao terceiro `>=30°C`, emite `critical`
e passa a `alerted=true`. Nesse estado, qualquer leitura `>=28°C` mantém alerta sem novos eventos,
valor `<28°C` emite `normalized` e reinicializa. Fora do alerta, valores de 28–29,999 e valores
não finitos zeram a sequência alta. Fontes operam com **um único sensor** por execução.

## Limites do escopo

Não há persistência, streaming de rede, autenticação, front-end, vários sensores ou tolerância
a falhas distribuídas. O objetivo é demonstrar eventos e avaliação de guardrails.
