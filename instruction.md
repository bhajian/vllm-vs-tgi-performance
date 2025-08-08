

oc project vllm-vs-tgi

oc create secret generic hf-secret \
  --from-literal=HUGGING_FACE_HUB_TOKEN=XXXXX


curl -k -s https://${HOST}/v1/chat/completions \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer ${HF_TOKEN}" \
  -d '{
        "model": "meta-llama/Meta-Llama-3-8B-Instruct",
        "messages": [
          { "role": "system", "content": "You are a concise assistant." },
          { "role": "user",   "content": "In one sentence, what is OpenShift?" }
        ],
        "max_tokens": 32,
        "temperature": 0.7
      }' | jq .




oc create sa tgi-sa -n default --dry-run=client -o yaml | oc apply -f -
oc adm policy add-scc-to-user anyuid -z tgi-sa -n default
# verify:
oc describe scc anyuid | grep 'system:serviceaccount:default:tgi-sa'

oc get secret hf-secret -n default 




